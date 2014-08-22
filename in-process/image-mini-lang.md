## Old but possibly interesting code

I was chatting with a co-worker the other day about stack based languages
and how, many years ago, I'd written a small one to control the image
manipulation via rewrite rules for a review site of mine.  At the time it
seemed like a bit of an indulgence, but in retrospect it was useful to have
a small language available for that sort of thing as my needs changed over
time.  So I thought I'd write a bit about that here.

So to start with, this is what a simple script in this language looks like:

`400,600,images/series/2328,!resize,jpg,images/sized/400/600/2328.jpg,!store`

The language is very simple.  We use commas to separate tokens.  Any tokens
starting with an exclamation mark is a command to execute.  Commands are
given a reference to the current stack and are expected to pop their
arguments off it and push their results on to it.

Here's the entire interpreter.  Everything except the command definitions
themselves:

```perl
my @stack;
foreach (split /,/, $ENV{'QUERY_STRING'}) {
    if (my( $command ) = /^!(.+)$/ ) {
        if ( my $sub = __PACKAGE__->can("fun_$command") ) {
            $sub->(\@stack);
        }
    } else {
        push @stack, $_;
    }
}
```
