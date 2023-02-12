# Design and Architecture

## Modules
Every module should expose an API interface.We treat interface methods as http calls.

Module API interface should accept Input objects and return output objects that are defined in module API namespace. Do not return entities or classes belonging to the module or expect entities or classes of other modules as input.

Module API interface cannot include actions because actions include transactions and when other modules need to use API interface of other modules they require control over transactions.

Modules can only call other module API interface methods in their Actions or Services.

```
Module A
  Api
    ModuleAInterface
      Dto
        Foo
        Bar
```

## Repository Pattern

Use repository pattern for all interaction with databases(including things like restore, force delete, etc.) or external APIs. Never directly call Eloquent methods in services, actions, etc.

Reasons:
- Improved testing. We can unit test repository methods then rely on mocks for feature tests that invlove repository calls. This way we should not have database assertions in feature tests(Unless extraordinery circumstances arise).
- Single source for all database transactions.

## Services

Services are not allowed to use transactions.

Services can use other services and other modules API interface but not Actions.

## Actions

Each controller method handles business logic using one Action.

Actions begin, commit and rollback transactions.

Actions use services, repository or service locator interface.

Actions must not return HTTP responses and may send raw data (for simple structure and types) or
action response DTO located in ``Modules/Actions/Responses``.

Bellow demonstrate simple relationship between controller and action:

**User/Http/Controllers/RegistrationController.php**
```php
public function store(RegistrationRequest $request, Register $action)
{
    $output = $action->execute($request);

    return new RegistrationResponse($output['user'], $output['token']);
}
```

**User/Actions/Register.php**
```php
/** @return array{user: User, token: string} */
public function execute(RegistrationRequest $request): array
{
    // ...

    $user = $this->userRepository->create($payload);

    $token = $this->authService->generateJWT();

    return compact('user', 'token');
}
```

## Controllers

Responsibilites:
- Handle Request validation using Form Requests.
- Call Actions to perform business logic.
- Handle Response structure using Response classes that extend `Illuminate\Http\JsonResponse` class. They should be in `HTTP\Responses` namespace. Example error response using Response class:
    ```php
    class PromotionUnavailableResponse extends JsonResponse
    {
        public function __construct(PromotionUnavailableException $exception)
        {
            $errorCode = $exception->getCode();

            parent::__construct(
                [
                    'message' => __(ErrorCodes::$translationKeys[$errorCode]),
                    'code' => $errorCode,
                ],
                400
            );
        }
    }
    ```
- Don't use database validation rules beause:
    - You slow down the happy path.
    - By default laravel doesn't use our Repositories so what we acheived by putting all database logic in Repository classes will be lost.
    - If under special circumstances database validation rules are required, use custom rules that use Repositories.

## Relationships

Enitities in one module can have relationships. Load relationships using parameters or special methods in repositories. Don't use `load()` after getting models from repositories.

```php
class OrderRepository
{
    public function getById(int $id, bool $withOrderProducts): Order;

    public function getByIdWithOrderProducts(int $id): Order;
}
```

Do not use Eloquent relationships across modules. Instead handle it manually in Service classes:

```php
$order = $this->orderRepository->getById($orderId, true);

$products = $this->productModuleInterface->getProductsByIds(
    $order->orderProducts->pluck('product_id')->toArray()
);

foreach ($order->getOrderProducts() as $$orderProduct) {
    $orderProduct->setProduct($products[$orderProduct->getProductId()]);
}
```
