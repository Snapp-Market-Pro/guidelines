# Testing

- Use PHPUnit.

- Tests should generally cover the public interface of projects, it can be public methods for a library, api endpoints for an API backend or Module Interfaces in a modular monolith.

- No hard rules on coverage but using it can help with removing unused code, etc.

- Feature tests for all API endpoints and Module API Interface methods.

- If a middleware is disabled in Feature tests make sure to unit test it.

- Use `#[Test]` attribute syntax for test cases and use snake case to make cases easier to read.
