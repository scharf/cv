```                                                                                                                                                                                                                                                                
From: sch...@EMBL-Heidelberg.DE (Michael Scharf)
Subject: Python and SNiFF+: browsing and debugging (was Re: Browsers, source editors, etc.)
Date: 1995/06/23
Message-ID: <scharfznceyimfbscg_-_@zinc.EMBL-Heidelberg.DE>
X-Deja-AN: 104895181
distribution: world
references: <9506220801.AA27803@kalle.teragon.ivab.se>
organization: European Molecular Biology Lab.
newsgroups: comp.lang.python
```

SUMMARY
-------

I have integrated Python into the SNiFF+ editing and browsing
environment. This includes extracting symbol information from Python
source code (class names, method names, instance variables, function
parameters, import statements ...). In addition I have written a new
debugger interface which works fine with SNiFF+. Now it's really fun
to develop Python with SNiFF+!

--------

Fredrik Lundh <Fredrik_Lu...@IVAB.SE> wrote:
> Just a little footnote...
> 
> > I agree (though i've never hear of SNiFF+ - odd name, that).
> > There's an idea that i presented at my "python programming
> > environment" session at the recent workshop, which i want to
> > mention.
> 
> SNiFF+ is a really good development environment targeted for C++,
> developed by TakeFive Software in Austria.  It integrates editing,
> documentation, structure browsing etc, and allows you to use your
> favorite tools (such as emacs, gcc) fully integrated into the
> environment.  It also seems to work fine for HUGE projects.  And of
> course, SNiFF+ is quite expensive.

Yes, but for universities there exist a special discount. For more
information send a mail to (Europe: sn...@takefive.co.at) or
(USA: i...@takefive.com). 

> But now for the really amusing stuff:
> 
> 	$ cd sniff2
> 	$ ls sniff2/lib
> 	python
> 	$ strings sniff2/bin/sniff | grep Copyright
> 	/.../
> 	Copyright 1991-1994 Stichting Mathematisch Centrum, Amsterdam
> 	/.../
> 
> Yeah right!  Python is everywhere. 

Yes everywhere :-) (That's what appears in the "About SNiFF" dialog.)

> That said, maybe someone from TakeFive is on this list, can write a
> much better plug for SNiFF+, and tell me if SNiFF+ even supports
> Python?  (At least, that would be nice for those of us that happens to
> use SNiFF+ :-)


The quick answer is: SNiFF+ is going to be opened for other
languages and there will be multi language support (see below).
Therefore an "open parser interface" (OPI) has been defined. It
will be available in the next minor release (which is scheduled by
TakeFive for end of July) of SNiFF+.

Since I'm an alpha-tester of the OPI and a very big Python fan and
user, I wrote a Python parser and debugger interface. And it works!
I will make it available with the next release of SNiFF+. I don't
think it makes sense to distribute this in the current state,
because with the current SNiFF+ you can have only one parser (and
therefore language) at a time: either C/C++ or Python. Also, I
consider my code 'alpha'.

David Ascher  <d...@maigret.cog.brown.edu> wrote:
>Feature Set: [this is what I'd want as a minimum]
>
>  -- python-aware editor, with indentation, tagged text,
>     documentation/version control support, syntax checking, etc.
>  -- a class hierarchy browser
>  -- a module browser to study existing modules (built-in and
>     otherwise)
>  -- a built-in debugger

BINGO! These are the features the SNiFF+ integration provides (except
for syntax checking)! Plus some more, like a class-browser, a
class-hierarchy-browser, a retriever (intelligent grep across a set
of files), configuration management (interface to RCS/SCCS/CVS/
ClearCase...), project management, interface to emacs (use emacs
instead the builtin editor), an integrated debugger interface etc.

I've been (and I am still!) a big emacs fan, I use emacs since 10
years, but for OO development SNiFF+ beats it because of its
specialized browsing tools.

OK, no more advertisement ;-), here is what I have done:

BROWSING PYTHON WITH SNiFF+
---------------------------

The current version of SNiFF+ (2.0.x) is very much C/C++ oriented,
but this will change in the next major release. Therefore I had to
squeeze the Python paradigms into the C/C++ model. Fortunately it
turns out that most of the important things we need for Python is
already in C++:

  - The (class-) hierarchy browser works fine.
  - In the symbol browser you can see all classes, methods,
    instance variables, functions and global variables.
  - In the class browser you can see all methods and instance
    variables of a class (and it's super classes). The methods are
    all public/virtual.
  - If you look at functions or methods with signature, you don't
    see the types of the parameters (which is a meaningless concept
    in Python). Instead, you'll see the names of the parameters.
  - With the retriever you can find the occurrence of any
    symbol/string.
  - From the editor you can jump to the definition of a selected
    symbol. If you want, only the imported modules are scanned (I
    fake import as C/C++ #include, so it always assumes everything
    is imported)

Still, there are some problems:
  - Where is an instance variable of a class declared? I just use
    all places where something is assigned to the variable. So
    instance variables may appear more then once in the browsing
    tools. The alternative would have been to take the first
    assignment as the declaration point.
  - All methods are public/virtual. 
  - Many of the C/C++ concepts make no sense for Python (macros,
    typedefs, non virtual methods, static members,
    public/private/protected...). Therefore there are many buttons
    in SNiFF+ which are unused for Python and should just not
    appear.
  - C/C++ does not have the concept of modules.
  - It does not (yet?) do cross-referencing. Anyway, I think this
    is almost impossible with a non statically typed language like
    Python, because you cannot determine which method is meant if
    you see something like x.f(). It could be any object defining a
    method f() or even worse, it could be something you cannot find
    out just steering at the source code (like eval or a builtin
    method or some class using __getattr__). But I'm open for good
    suggestions.
  - Since I'm an emacs freak, I faked the font-lock-mode by
    declaring strings as macros and language keywords as
    typedefs. So SNiFF+ will highlight them in the editor, which
    makes the code (at least for me) much more readable.
  - Currently you can either run a C/C++ parser or the Python
    parser. You have to quit SNiFF+ to use another language. But
    this will change with the end of July version of SNiFF+.
  - To my surprise, SNiFF+ has no problems with a method overridden
    in a subclass having different parameter names. Since I use the
    parameter names as the parameter types, SNiFF+ should recognize
    a method with different names as an overloaded method and not as
    overridden. Due to a bug (or a feature :-) it checks only for
    the parameters to distinguish two (potentially overloaded)
    methods if there is more then one method defined within the
    current class (not considering the super class).  (I think
    gcc-2.5.8 had a similar bug).
  - I assume that self in methods is really called self. This
    should changed, but for now, it is a good first approximation
    since most people stick to that convention.
  - Class variables are not recognized. I mean in 'class X: y=42' y
    is not found as member of instances of X. Except if somewhere
    in a method of X there is an assignment like 'self.y=0'.

HOW IT HAS BEEN DONE
--------------------

To extract the symbol information (like variables, class, class
name etc) there is a parser running in background which is
activated each time a file changes on disk. It then feeds SNiFF+
with the start and end position of each symbol in the file. In some
cases you have to give some additional information like the list of
parameters or super-classes. But all in all it's quite simple
(unless you want to support cross-referencing). Currently only one
such parser (the C/C++ parser) can talk to SNiFF+ using
sockets. But as I have said, this will change...

I wrote a simple (fuzzy) parser using only flex with exclusive
states. To my surprise it works fine even in very tricky cases. And
it is fast.

Before this, I've been experimenting with a parser written in
Python, but it turned out, that it is far too slow -sigh-. So now
I'm experimenting with a lex/yacc-like builtin for Python. My first
experiments show, that this is about one order of magnitude faster
then something purely written in Python. As reference language I
used SGML/HTML and compare the speed to HTML-lib.py.

THE FUTURE OF THE PYTHON INTEGRATION
------------------------------------

It would be nice to teach SNiFF+ about builtin classes. If a parser
would parse the Python-C-API it could generate artificial classes
which then can be browsed and put into the class hierarchy.

Since there are two ways of inheritance, class inheritance and
delegation inheritance, it'd be nice to also support delegation
inheritance. What do I mean by this? Let's assume you would want to
extend the builtin file class. A simple wrapper would help:

    class MyFile
        """A file that returns uppercased lines.
    
        HINT: superclass=file"""
        def __init__(self,file):
            self._file_=file
        def __getattr__(self,name):
            return getattr(self._file_,name)
        def readline():
            line=self._file_.readline()
            from string import upper
            return upper(line)
    
It should appear as a subclass of file overriding the method
readline. Maybe with the assistance of some structured comments or
docstrings.

SNiFF+ should support some of the Python concepts better: like
modules, no static (compile time) typed variables etc.

Get some ideas how one could generate Python cross-reference
information.

Convert (automatically) the Python documentation to the
Documentation Browser format (which, in the long term, may become
SGML/HTML).

DEBUGGER INTEGRATION
-------------------

Since developing without debugging is a pain, I decided to
integrate the Python debugger into SNiFF+. SNiFF+ does the
following: It runs a command line debugger in a pty and interprets
its output (e.g. where a break point has been set or the stack
trace) and if you hit a button or a menu entry, it types something
to the debugger. To adapt to a new debugger you have to write a
debugger adaptor (in C++) which matches the output of the debugger
and does a translation of the internal (menu/button) commands
(step, break, run etc) to some strings typed at the debugger
prompt. Since I prefer writing Python code over writing C++ code, I
decided to write a new Python debugger that fakes the input and
output of the gdb (GNU debugger) for which an adaptor already
exists.

In order to make it really act like the gdb, I changed some of the
commands (input and output) + I wrote a new cmd.py (I call it
cmdl.py) which uses docstrings to find out about documentation of
the functions...  (BTW. that's one of the things I use the
readlinemodule.c for, which I posted a few days ago :-). In
addition, it is not necessary to change any Python code to run the
debugger.  Instead, I read the file to be debugged and eval it from
the debugger faking it would run in the module __main__.

With all this it is really fun to use the debugger, because
breakpoints are set in the source editor, you can step through the
code and print whatever you like to see ... In fact, before I had
this interface I didn't use the debugger at a lot, but now it's so
much easier :-).

I have not tried yet, but I think one could use this debugger also
from within emacs as a Python replacement of gdb. Before I make my
pydb available I'll test this as well...

What I'm also thinking of is a mode where you can have both
debuggers (gdb and pydb) simultaneously and you can set break
points either in Python or in the C/C++ code.... well it's maybe
not trivial but it would be nice.

TakeFive, SNiFF+ AND Python
---------------------------

In SNiFF+ Python is currently only used to extend the editor. It is
considered as a first step and has therefore not been made
public. There has been quite some discussion which extension
language to use for SNiFF+ and it is not clear if Python is the
best choice. The mayor argument against Python and for [incr TCL]
is that TCL is much better known (a wider user basis) and it has a
better chance of survival because of this and maintained by SUN. On
the other hand (and that is my argument), Python is the *much*
nicer and cleaner language. 

So folks, how would you convince TakeFive to continue using the non
mainstream language Python instead of TCL? Or is Java the future?
(No language wars, please, just some good arguments for Python :-)




Michael

P.S.: Don't be surprised if my e-mail address will change in the
near future to Michael_Sch...@TakeFive.co.at ...

```
--
 ___   _  _   ___   _    
| __) | \/ | |   ) | |   Michael Scharf  Tel: +49 6221 387 305 FAX: 517
| _)  |    | | -<  | |_  EMail: Michael.Sch...@EMBL-Heidelberg.de
|___) |_||_| |___) |___) http://www.EMBL-Heidelberg.de/~scharf/

```