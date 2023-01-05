# Testing

Use PHPUnit.

No hard rules on coverage but using it can help with removing unused code, etc.

Try to write Unit Tests for Repositories using database assertions.

Feature tests for all API endpoints and Module API Interface methods.

If a middleware is disabled in Feature tests make sure to unit test it.

Test relationship retrun types.

Use `/** @test */` syntax for test cases and use snake case to make cases easier to read.
