---
layout: default
title: Perl notes
---

## Executing system commands synchronously


### [Fire and forget with system](https://www.effectiveperlprogramming.com/2010/04/when-perl-isnt-enough)

[perlfunc](http://perldoc.perl.org/functions/system.html)

If you don’t care about the output, you can use system to start a command. It’s output goes to the same place your Perl output goes to (e.g. to the screen):

```pl
system( 'ls', '-l' )
	and die 'something went horribly wrong';
```

Your perl program waits (“blocks”) while the external command runs, and your perl program only continues once the external command completes.

The return value from system is the exit value from the external command. Each command can define its own exit values, but most commonly they return 0 for success or a non-zero number to indicate a particular sort of failure. At first glance the _and_ looks odd, but that’s because an error from ls returns a value that Perl considers true.

### [``]()

[perlop](http://perldoc.perl.org/perlop.html)

Backticks capture standard output of the command run and perl program execution is blocked.

Backticks also trigger the same interpolation that double-quotes do, so you can place variables in the backticks and they will be converted into text before Perl executes the command.

If you use the backticks in scalar context, Perl returns all of the standard output (but not standard error!) as a single, possibly multi-line string. If you use it in list context, then Perl returns a list that has one line per element:

```pl
# Full directory listing in a single scalar
my $output = `ls -l`;

# Full directory listing, one line per list item
my @output = `ls -l`;
```

The backticks have a generalized quoting syntax too. The qx{} quoting is the same as the literal `` but allows you to choose the delimiter (see perlop). These are all the same code:

```pl
my $output = `ls -l`;

my $output = qx{ls -l};

my $output = qx(ls -l);
```

## [Perl Traps](http://mojolicious.org/perldoc/perltrap)

Practicing Perl Programmers should take note of the following:

* Remember that many operations behave differently in a list context than they do in a scalar one. See perldata for details.

* Avoid barewords if you can, especially all lowercase ones. You can't tell by just looking at it whether a bareword is a function or a string. By using quotes on strings and parentheses on function calls, you won't ever get them confused.

* You cannot discern from mere inspection which builtins are unary operators (like chop() and chdir()) and which are list operators (like print() and unlink()). (Unless prototyped, user-defined subroutines can only be list operators, never unary ones.) See perlop and perlsub.

* People have a hard time remembering that some functions default to $_, or @ARGV, or whatever, but that others which you might expect to do not.

* The <FH> construct is not the name of the filehandle, it is a readline operation on that handle. The data read is assigned to $_ only if the file read is the sole condition in a while loop:

```pl
while (<FH>)*   { }
while (defined($_ = <FH>)) { }..
<FH>;  # data discarded!
```

* Remember not to use = when you need =~; these two constructs are quite different:

```pl
$x =  /foo/;
$x =~ /foo/;
```

* The do {} construct isn't a real loop that you can use loop control on.

* Use my() for local variables whenever you can get away with it (but see perlform for where you can't). Using local() actually gives a local value to a global variable, which leaves you open to unforeseen side-effects of dynamic scoping.

* If you localize an exported variable in a module, its exported value will not change. The local name becomes an alias to a new value but the external name is still an alias for the original.

As always, if any of these are ever officially declared as bugs, they'll be fixed and removed.

### Shell Traps

Sharp shell programmers should take note of the following:

* The backtick operator does variable interpolation without regard to the presence of single quotes in the command.

* The backtick operator does no translation of the return value, unlike csh.

* Shells (especially csh) do several levels of substitution on each command line. Perl does substitution in only certain constructs such as double quotes, backticks, angle brackets, and search patterns.

* Shells interpret scripts a little bit at a time. Perl compiles the entire program before executing it (except for BEGIN blocks, which execute at compile time).

* The arguments are available via @ARGV, not $1, $2, etc.

* The environment is not automatically made available as separate scalar variables.

* The shell's test uses "=", "!=", "<" etc for string comparisons and "-eq", "-ne", "-lt" etc for numeric comparisons. This is the reverse of Perl, which uses eq, ne, lt for string comparisons, and ==, != < etc for numeric comparisons.

