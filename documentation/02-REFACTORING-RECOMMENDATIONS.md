# Refactoring Recommendations - Snipe-IT Modernization Strategy

## Overview

This document provides detailed refactoring strategies to modernize Snipe-IT from a legacy monolithic application to a well-architected, maintainable system following SOLID principles and Laravel best practices.

---

## Phase 1: Foundation (Weeks 1-2)

### 1.1 Implement Comprehensive Testing Framework

#### Objective
Establish test infrastructure to enable safe refactoring

#### Tasks
1. **Set Up Testing Tools**
   ```bash
   composer require --dev phpunit/phpunit
   composer require --dev phpstan/phpstan
   composer require --dev laravel/pint
   ```

2. **Create Test Structure**
   ```
   tests/
   ├── Feature/          # Integration tests
   ├── Unit/             # Unit tests
   └── TestCase.php      # Base test class
   ```

3. **Write Integration Tests First**
   - Test critical user journeys
   - Test API endpoints
   - Test checkout/checkin processes
   - Test reporting functionality

#### Expected Coverage After Phase 1: 30%

---

### 1.2 Implement Global Exception Handling

#### Current Problem
```php
// Controllers throw exceptions inconsistently
throw new Exception("Asset not found");
throw new InvalidArgumentException("Invalid user");
return response()->json(['error' => 'Not found'], 404);
```

#### Solution
```php
// Create custom exceptions
namespace App\Exceptions;

class AssetNotFoundException extends Exception {}
class UnauthorizedException extends Exception {}

// In Handler.php
public function render($request, Throwable $exception)
{
    if ($exception instanceof AssetNotFoundException) {
        return response()->json(['error' => 'Asset not found'], 404);
    }
    
    if ($exception instanceof UnauthorizedException) {
        return response()->json(['error' => 'Unauthorized'], 403);
    }
    
    // ... handle other exceptions
}
```

#### Benefits
- Consistent error responses across API
- Centralized error handling logic
- Better error logging and monitoring

---

### 1.3 Add API Rate Limiting

#### Implementation
```php
// routes/api.php
Route::middleware(['auth:api', 'throttle:60,1'])->group(function () {
    Route::apiResource('assets', AssetController::class);
    Route::apiResource('licenses', LicenseController::class);
});
```

#### Benefits
- Prevent abuse
- Protect against DDoS attacks
- Fair resource allocation

---

## Phase 2: Service Layer Extraction (Weeks 3-4)

### 2.1 Create Service Layer

#### Problem
Business logic scattered in controllers

#### Solution: Service Classes

```php
// app/Services/AssetService.php
namespace App\Services;

class AssetService
{
    public function __construct(
        private AssetRepository $repository,
        private LogService $logService
    ) {}

    public function checkoutAsset(int $assetId, int $userId): CheckoutRecord
    {
        // Validate asset availability
        $asset = $this->repository->findAvailable($assetId);
        
        // Create checkout record
        $checkout = $this->repository->createCheckout($asset, $userId);
        
        // Update asset status
        $asset->markAsCheckedOut();
        
        // Log action
        $this->logService->logCheckout($asset, $userId);
        
        return $checkout;
    }

    public function checkinAsset(int $checkoutId): void
    {
        $checkout = $this->repository->findCheckout($checkoutId);
        $asset = $checkout->asset;
        
        $asset->markAsAvailable();
        $checkout->complete();
        
        $this->logService->logCheckin($asset, $checkout->user_id);
    }
}
```

#### Refactored Controller
```php
// app/Http/Controllers/AssetController.php
class AssetController extends Controller
{
    public function __construct(private AssetService $service) {}

    public function checkout(Request $request, Asset $asset)
    {
        $checkout = $this->service->checkoutAsset(
            $asset->id,
            $request->user()->id
        );

        return response()->json($checkout);
    }

    public function checkin(Request $request, Checkout $checkout)
    {
        $this->service->checkinAsset($checkout->id);
        
        return response()->json(['success' => true]);
    }
}
```

#### Benefits
- Testable business logic
- Reusable across endpoints
- Clear separation of concerns

---

### 2.2 Implement Repository Pattern

#### Problem
Direct database queries scattered throughout code

#### Solution: Repository Abstraction

```php
// app/Repositories/AssetRepository.php
namespace App\Repositories;

interface AssetRepositoryInterface
{
    public function findAvailable(int $id): Asset;
    public function findCheckedOut(int $userId): Collection;
    public function findByCategory(int $categoryId): Collection;
    public function create(array $data): Asset;
    public function update(int $id, array $data): Asset;
    public function delete(int $id): bool;
}

class EloquentAssetRepository implements AssetRepositoryInterface
{
    public function findAvailable(int $id): Asset
    {
        return Asset::where('id', $id)
            ->where('status', AssetStatus::AVAILABLE)
            ->firstOrFail();
    }

    public function findCheckedOut(int $userId): Collection
    {
        return Asset::whereHas('checkouts', function ($query) use ($userId) {
            $query->where('user_id', $userId)->whereNull('to_date');
        })->get();
    }

    // ... other methods
}
```

#### Binding in Service Provider
```php
// app/Providers/RepositoryServiceProvider.php
$this->app->bind(
    AssetRepositoryInterface::class,
    EloquentAssetRepository::class
);
```

#### Benefits
- Easy to swap implementations
- Easier to test (mock repositories)
- Database-agnostic business logic

---

## Phase 3: Code Quality Improvements (Weeks 5-6)

### 3.1 Extract Duplicate Code

#### Problem
Same validation/processing logic in multiple places

#### Solution: Traits & Base Classes

```php
// app/Traits/Loggable.php
trait Loggable
{
    protected function logAction(
        string $action,
        string $table,
        int $recordId,
        array $changes
    ): void {
        AuditLog::create([
            'user_id' => auth()->id(),
            'action' => $action,
            'table_name' => $table,
            'record_id' => $recordId,
            'new_values' => $changes,
        ]);
    }
}

// Usage in Services
class AssetService
{
    use Loggable;

    public function updateAsset(int $id, array $data): Asset
    {
        $asset = Asset::findOrFail($id);
        $original = $asset->toArray();
        
        $asset->update($data);
        
        $this->logAction('update', 'assets', $id, $data);
        
        return $asset;
    }
}
```

### 3.2 Implement Form Requests

#### Problem
Validation scattered in controllers or missing

#### Solution: Dedicated Form Requests

```php
// app/Http/Requests/CheckoutAssetRequest.php
namespace App\Http\Requests;

class CheckoutAssetRequest extends FormRequest
{
    public function authorize(): bool
    {
        return auth()->check();
    }

    public function rules(): array
    {
        return [
            'asset_id' => 'required|exists:assets,id',
            'from_date' => 'required|date|after:today',
            'to_date' => 'required|date|after:from_date',
            'reason' => 'sometimes|string|max:500',
        ];
    }

    public function messages(): array
    {
        return [
            'asset_id.exists' => 'The selected asset does not exist.',
            'from_date.after' => 'Checkout date must be in the future.',
        ];
    }
}

// Usage in Controller
class CheckoutController extends Controller
{
    public function store(CheckoutAssetRequest $request)
    {
        // $request->validated() returns only validated data
        $checkout = Checkout::create($request->validated());
        
        return response()->json($checkout);
    }
}
```

### 3.3 Add PHPDoc Comments

#### Standard
```php
/**
 * Checkout an asset to a user
 *
 * @param int $assetId The ID of the asset to checkout
 * @param int $userId The ID of the user checking out the asset
 * @return CheckoutRecord The created checkout record
 * @throws AssetNotFoundException If asset doesn't exist
 * @throws AssetNotAvailableException If asset is already checked out
 */
public function checkoutAsset(int $assetId, int $userId): CheckoutRecord
{
    // ...
}
```

---

## Phase 4: Architecture Modernization (Weeks 7-8)

### 4.1 Implement Event-Driven Architecture

#### Problem
Direct dependencies between services

#### Solution: Events & Listeners

```php
// app/Events/AssetCheckedOut.php
namespace App\Events;

class AssetCheckedOut
{
    public function __construct(
        public Asset $asset,
        public User $user,
        public Checkout $checkout
    ) {}
}

// app/Listeners/SendCheckoutNotification.php
class SendCheckoutNotification
{
    public function handle(AssetCheckedOut $event): void
    {
        Mail::send(new CheckoutNotification(
            $event->user,
            $event->asset,
            $event->checkout
        ));
    }
}

// Usage in Service
class AssetService
{
    public function checkoutAsset(int $assetId, int $userId): CheckoutRecord
    {
        // ... checkout logic
        
        event(new AssetCheckedOut($asset, $user, $checkout));
        
        return $checkout;
    }
}
```

#### Benefits
- Loose coupling
- Easy to add new functionality
- Reusable event listeners

---

### 4.2 Implement Query Objects (CQRS Pattern)

#### Problem
Complex query logic scattered in controllers/repositories

#### Solution: Query Objects

```php
// app/Queries/GetUserAssetsQuery.php
class GetUserAssetsQuery
{
    public function __construct(
        private int $userId,
        private ?int $categoryId = null,
        private ?string $status = null,
        private int $perPage = 15
    ) {}

    public function get(): LengthAwarePaginator
    {
        $query = Asset::where('assigned_to', $this->userId);

        if ($this->categoryId) {
            $query->where('category_id', $this->categoryId);
        }

        if ($this->status) {
            $query->where('status', $this->status);
        }

        return $query->paginate($this->perPage);
    }
}

// Usage in Controller
class AssetController extends Controller
{
    public function userAssets(Request $request)
    {
        $query = new GetUserAssetsQuery(
            $request->user()->id,
            $request->input('category_id'),
            $request->input('status')
        );

        return response()->json($query->get());
    }
}
```

---

### 4.3 API Versioning Strategy

#### Implementation
```php
// routes/api.php
Route::prefix('v1')->group(function () {
    Route::apiResource('assets', V1\AssetController::class);
    Route::apiResource('licenses', V1\LicenseController::class);
});

Route::prefix('v2')->group(function () {
    Route::apiResource('assets', V2\AssetController::class);
    // v2 has different response structure, additional fields, etc.
});
```

#### Benefits
- Backward compatibility
- Gradual migration to new API
- Support for multiple client versions

---

## Implementation Priority

### Quick Wins (1 week)
- [x] Global exception handling
- [x] API rate limiting
- [x] Form request validation
- [x] Basic testing setup

### High Priority (2 weeks)
- [ ] Extract service layer
- [ ] Implement repository pattern
- [ ] Extract duplicate code
- [ ] Add critical path tests

### Medium Priority (2 weeks)
- [ ] Event-driven architecture
- [ ] Query objects
- [ ] API versioning
- [ ] Comprehensive documentation

### Low Priority (ongoing)
- [ ] Performance optimization
- [ ] Database partitioning
- [ ] Advanced caching
- [ ] Microservices extraction

---

## Success Metrics

### After Phase 1 (Week 2)
- ✅ Test infrastructure in place
- ✅ 30% test coverage
- ✅ Global error handling implemented

### After Phase 2 (Week 4)
- ✅ 50% test coverage
- ✅ Service layer extracted
- ✅ Repository pattern implemented
- ✅ 30% complexity reduction

### After Phase 3 (Week 6)
- ✅ 70% test coverage
- ✅ No duplicate code
- ✅ Consistent validation
- ✅ Comprehensive documentation

### After Phase 4 (Week 8)
- ✅ 85% test coverage
- ✅ Event-driven architecture
- ✅ Query objects implemented
- ✅ API versioning strategy in place

---

## Estimated Timeline & Resources

| Phase | Duration | Dev Days | Developers |
|-------|----------|----------|------------|
| Phase 1: Foundation | 2 weeks | 10 | 1-2 |
| Phase 2: Service Layer | 2 weeks | 12 | 1-2 |
| Phase 3: Quality | 2 weeks | 10 | 1 |
| Phase 4: Modernization | 2 weeks | 12 | 1-2 |
| **Total** | **8 weeks** | **44** | **1-2** |

---

## Conclusion

This refactoring strategy provides a roadmap to modernize Snipe-IT while maintaining functionality and minimizing risk. By following the phased approach, the codebase will gradually improve in quality, testability, and maintainability.
