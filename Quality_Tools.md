# Quality Tools

## PhpStan
- Use PhpStan for static analysis.
- Level 8 is a good default as it is much more feasible than level 9 yet strict enough that it can stop many major bugs.

## PHP-CS-FIXER
php-cs-fixer config:
```php
[
    '@PSR12' => true,
    '@PSR12:risky' => true,
    'strict_param' => true,
    'declare_strict_types' => true,
    'array_syntax' => ['syntax' => 'short'],
    'ordered_imports' => [
        'imports_order' => null,
        'sort_algorithm' => 'alpha',
    ],
    'phpdoc_types_order' => [
        'null_adjustment' => 'always_last',
        'sort_algorithm' => 'alpha',
    ],
    'multiline_whitespace_before_semicolons' => [
        'strategy' => 'no_multi_line',
    ],
    'comment_to_phpdoc' => true,
    'no_empty_phpdoc' => true,
    'no_superfluous_phpdoc_tags' => [
        'allow_mixed' => true,
        'allow_unused_params' => false,
        'remove_inheritdoc' => true,
    ],
    'array_indentation' => true,
    'binary_operator_spaces' => ['default' => 'single_space'],
    'concat_space' => ['spacing' => 'one'],
    'no_unused_imports' => true,
    'phpdoc_scalar' => true,
    'simple_to_complex_string_variable' => true,
    'single_quote' => [
        'strings_containing_single_quote_chars' => true,
    ],
    'class_attributes_separation' => [
        'elements' => ['method' => 'one']
    ],
    'modernize_types_casting' => true,
]
```
