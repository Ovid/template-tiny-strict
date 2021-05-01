# NAME

Template::Tiny::Strict - Template Toolkit reimplemented in as little code as possible

# VERSION

version 1.16

# SYNOPSIS

```perl
my $template = Template::Tiny::Strict->new(
    TRIM          => 1,
    forbid_undef  => $optional_boolean,
    forbid_unused => $optional_boolean,
);

# Print the template results to STDOUT
$template->process( <<'END_TEMPLATE', { foo => 'World' } );
Hello [% foo %]!
END_TEMPLATE

# Fatal: Unused variable
$template->process( <<'END_TEMPLATE', { foo => 'World', bar => 'Hello' } );
Hello [% foo %]!
END_TEMPLATE

# Fatal: Undefined variable
$template->process( <<'END_TEMPLATE', { foo => undef } );
Hello [% foo %]!
END_TEMPLATE
```

# DESCRIPTION

**Template::Tiny::Strict** is a drop-in replacement for [Template::Tiny](https://metacpan.org/pod/Template::Tiny). By default,
the behavior is identical. However, we have two new _optional_ arguments you can pass
to the constructor:

- `forbid_undef`

    If true, _any_ access of an undefined value in the template will cause the code to `croak`
    with an error such as:

    ```
    Undefined value in template path 'items.1'
    ```

- `forbid_unused`

    If true, _any_ variable passed in the stash that is not used will cause the coad to
    `croak` with an error such as:

    ```perl
    The following variables were passed to the template but unused: 'name'
    ```

As a convenience, all errors are gathered and reported at once.

**Note**: what follows is the remainder of the original POD.

It is intended for use in light-usage, low-memory, or low-cpu templating
situations, where you may need to upgrade to the full feature set in the
future, or if you want the retain the familiarity of TT-style templates.

For the subset of functionality it implements, it has fully-compatible template
and stash API. All templates used with **Template::Tiny::Strict** should be able to be
transparently upgraded to full Template Toolkit.

Unlike Template Toolkit, **Template::Tiny::Strict** will process templates without a
compile phase (but despite this is still quicker, owing to heavy use of
the Perl regular expression engine.

## SUPPORTED USAGE

Only the default `[% %]` tag style is supported.

Both the `[%+ +%]` style explicit whitespace and the `[%- -%]` style
explicit chomp **are** support, although the `[%+ +%]` version is unneeded
in practice as **Template::Tiny::Strict** does not support default-enabled `PRE_CHOMP`
or `POST_CHOMP`.

Variable expressions in the form `[% foo.bar.baz %]` **are** supported.

Appropriate simple behaviours for `ARRAY` references, `HASH` references and
objects are supported. "VMethods" such as \[% array.length %\] are **not**
supported at this time.

`IF`, `ELSE` and `UNLESS` conditional blocks **are** supported, but only with
simple `[% foo.bar.baz %]` conditions.

Support for looping (or rather iteration) is available in simple
`[% FOREACH item IN list %]` form **is** supported. Other loop structures are
**not** supported. Because support for arbitrary or infinite looping is not
available, **Template::Tiny::Strict** templates are not turing complete. This is
intentional.

All of the four supported control structures `IF`/`ELSE`/`UNLESS`/`FOREACH`
can be nested to arbitrary depth.

The treatment of `_private` hash and method keys is compatible with
[Template](https://metacpan.org/pod/Template) Toolkit, returning null or false rather than the actual content
of the hash key or method.

Anything beyond the above is currently out of scope.

# METHODS

## new

```perl
my $template = Template::Tiny::Strict->new(
    TRIM => 1,
);
```

The `new` constructor is provided for compatibility with Template Toolkit.

The only parameter it currently supports is `TRIM` (which removes leading
and trailing whitespace from processed templates).

Additional parameters can be provided without error, but will be ignored.

## process

```perl
# DEPRECATED: Return template results (emits a warning)
my $text = $template->process( \$input, $vars );

# Print template results to STDOUT
$template->process( \$input, $vars );

# Generate template results into a variable
my $output = '';
$template->process( \$input, $vars, \$output );
```

The `process` method is called to process a template.

The first parameter is a reference to a text string containing the template
text. A reference to a hash may be passed as the second parameter containing
definitions of template variables.

If a third parameter is provided, it must be a scalar reference to be
populated with the output of the template.

For a limited amount of time, the old deprecated interface will continue to
be supported. If `process` is called without a third parameter, and in
scalar or list contest, the template results will be returned to the caller.

If `process` is called without a third parameter, and in void context, the
template results will be `print()`ed to the currently selected file handle
(probably `STDOUT`) for compatibility with [Template](https://metacpan.org/pod/Template).

# SEE ALSO

[Config::Tiny](https://metacpan.org/pod/Config::Tiny), [CSS::Tiny](https://metacpan.org/pod/CSS::Tiny), [YAML::Tiny](https://metacpan.org/pod/YAML::Tiny)

# AUTHOR

Adam Kennedy <adamk@cpan.org>

# COPYRIGHT AND LICENSE

This software is copyright (c) 2009 by Adam Kennedy.

This is free software; you can redistribute it and/or modify it under
the same terms as the Perl 5 programming language system itself.
