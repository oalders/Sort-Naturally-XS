## NAME

Sort::Naturally::XS - Perl extension for human-friendly ("natural") sort order

## INSTALL

To install this module type the following, for Unix-like OS:
```
   perl Makefile.PL
   make
   make test
   make install
```

or for Windows:
```
   perl Makefile.PL
   dmake
   dmake test
   dmake install
```

## SYNOPSIS

```perl
  use Sort::Naturally::XS;

  my @mixed_list = qw/test21 test20 test10 test11 test2 test1/;

  my @result = nsort(@mixed_list); # @result is: test1 test2 test10 test11 test20 test21

  @result = sort ncmp @mixed_list; # same, but use standard sort function

  @result = sort {ncmp($a, $b)} @mixed_list; # same as ncmp, but argument pass explicitly
  
  my $result = Sort::Naturally::XS::sorted(\@mixed_list, locale => 'ru_RU.utf8'); # pass custom locale
```

## DESCRIPTION

Natural sort order is an ordering of mixed strings (consist of characters and digits) in alphabetical order, except that
digital parts are ordered as numbers. Natural sorting can be considered as a replacement of a standard machine-oriented
alphabetical sorting, because it is more convenient for human understanding. For example, the following list:

```perl
  test21 test20 test10 test11 test2 test1
```

after performing a standard machine-oriented alphabetical sorting, will be as follows:

```perl
  test1 test10 test11 test2 test20 test21
```

The sequence appears unnatural, because test10 and test11 come before test2. On the other hand, natural sorting gives a
human-friendly sequence:

```perl
  test1 test2 test10 test11 test20 test21
```

now test2 comes before test10 and test11.

## METHODS

#### `ncmp`

> ncmp(LEFT, RIGHT)

Replacement of the `cmp` standard operator. LEFT and RIGHT lines are presented for comparison. Returns 1 if LEFT should
come before RIGHT, -1 if RIGHT should come before LEFT and 0 if LEFT and RIGHT match.

```perl
  # sort @list naturally, support in latest perl versions
  my @result = sort ncmp @list;

  # same, but arguments pass explicitly
  @result = sort {ncmp($a, $b)} @list;

  # more complex example, sort ARRAY of HASH refs by key 'foo' in descending order
  @result = sort {ncmp($b->{foo}, $a->{foo})} @list;
```

#### `nsort`

> nsort(LIST)

In list context returns a LIST sorted copy.

```perl
  my @result = nsort(@list);
```

#### `sorted`

> sorted(ARRAY_REF, KWARGS)

Returns an ARRAY ref to a sorted list. First argument is an ARRAY ref to the source list, followed by keyword arguments,
such as `reverse` and `locale`. If `reverse` is true the source list is sorted in reverse order. If `locale` is
specified, the sorting will be performed according to the locale aware settings.

```perl
  use Sort::Naturally::XS qw/sorted/;

  my $result = sorted($list);

  $result = sorted($list, reverse => 1); # $list will be sorted in descending order

  $result = sorted($list, locale => 'en_US.utf8'); # $list will be sorted according to en_US.utf8 locale
```

## LOCALE AWARE SORTING

By default the `sort` function sorts according to a standard C locale or, if a `use locale` pragma is in effect,
according to OS settings, which can be changed with the help of the `setlocale` function. The use of both `use locale`
and `setlocale` has no effect on `ncmp` and `nsort`. The following example demonstrates this behavior:

```perl
  use POSIX;
  use Sort::Naturally::XS;

  my @list = ('a.'.'c', 'A'..'B');

  my @result_std = sort @list;
  my @result_ncmp = sort {ncmp($a, $b)} @list;
  # @result_std contains  A, B, C, a, b, c
  # @result_ncmp contains A, B, C, a, b, c

  use locale;
  # assumed that current locale is en_US.utf8
  @result_std = sort @list;
  @result_ncmp = sort {ncmp($a, $b)} @list;
  # @result_std contains  a, A, b, B, c, C
  # @result_ncmp contains A, B, C, a, b, c

  setlocale(POSIX::LC_ALL, 'en_CA.utf8');
  @result_std = sort @list;
  @result_ncmp = sort {ncmp($a, $b)} @list;
  # @result_std contains  A, a, B, b, C, c
  # @result_ncmp contains A, B, C, a, b, c
```

To be able to sort a list with an arbitrary locale it is necessary to use the `sorted` function with a `locale` keyword
argument:

```perl
  use Sort::Naturally::XS qw/sorted/;

  my $list = ['a.'.'c', 'A'..'B'];

  my $result_us = sorted($list, locale => 'en_US.utf8');
  # $result_us contains a, A, b, B, c, C

  my $result_ca = sorted($list, locale => 'en_CA.utf8');
  # $result_ca contains A, a, B, b, C, c
```

Note: due to the complexity of a cross-platform support, a locale aware sorting is guaranteed on Unix-like operating
systems only.

## EXPORT

By default the module exports `ncmp` and `nsort` subroutines.

## NOTES

* There are differences in sorting outcomes compared with the `Sort::Naturally` module. Capital letters always come
before lower case letters, digits always come before letters.
* Due to a significant strain it is not recommended for sorting lists consisting of letters or digits only.
* Due to the complexity of a cross-platform support, a locale aware sorting is guaranteed on Unix-like operating systems
only.
* Windows support added in ver. 0.7.6

## SEE ALSO

* [module on PrePAN](http://prepan.org/module/nYcMhBVby72)
* [module on CPAN](http://search.cpan.org/dist/Sort-Naturally-XS/lib/Sort/Naturally/XS.pm)

## AUTHOR

Sergey Yurzin, [jurzin.s@gmail.com](mailto:jurzin.s@gmail.com)

## COPYRIGHT AND LICENSE

Copyright (C) 2017 by Sergey Yurzin

This library is free software; you can redistribute it and/or modify
it under the same terms as Perl itself, either Perl version 5.18.2 or,
at your option, any later version of Perl 5 you may have available.
