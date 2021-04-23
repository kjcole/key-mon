# key-mon

## 2020.08.01 (KJC)

Starting from scratch... Sort of.

I'm once again torn between trying to do everything via Debian
packages (`apt`) or everything via Python packages (`pip`).
Ultimately, I decided to reduce dependence on a particular Linux
distribution, and instead go for Python packages as much as possible.

I set up a virtual environment with `pipenv` and attempted to run
`key-mon` "cold".

    $ cd src
    $ ./key-mon

It failed to import `cairo`, `gi`, and `xlib`.

```
    $ pipenv install pycairo
    $ pipenv install PyGObject
    Failed...

    $ sudo apt install libgirepository1.0-dev
    The following additional packages will be installed:
      gobject-introspection python3-mako
    Suggested packages:
      libgirepository1.0-doc python3-beaker python-mako-doc
    The following NEW packages will be installed:
      gobject-introspection libgirepository1.0-dev python3-mako
    0 upgraded, 3 newly installed, 0 to remove and 0 not upgraded.
    Need to get 1,104 kB of archives.
    After this operation, 12.4 MB of additional disk space will be used.

    $ pipenv install PyGObject
    $ pipenv install xlib

    $ pip list
    Package    Version
    ---------- -------
    pip        20.1.1
    pycairo    1.19.1
    PyGObject  3.36.1
    setuptools 47.1.1
    six        1.15.0
    wheel      0.34.2
    xlib       0.21
    $
```

Success.

Still, it would be nice to know if any additional Debian packages that
I'd previously installed are hidden dependencies. So, a virtual machine
with a minimal Linux might be called for.

----

## 2020.08.02 (KJC)

On a virtual machine with a bare minimum Lubuntu installation...

```
    $ sudo apt install python3-pip
    [sudo] password for kjcole:
    Reading package lists... Done
    Building dependency tree
    Reading state information... Done
    The following packages were automatically installed and are no longer
    required:
      efibootmgr libegl1-mesa libfwup1 libllvm9
    Use 'sudo apt autoremove' to remove them.
    The following additional packages will be installed:
      binutils binutils-common binutils-x86-64-linux-gnu build-essential
      dh-python dpkg-dev fakeroot g++ g++-7 gcc gcc-7
      libalgorithm-diff-perl libalgorithm-diff-xs-perl
      libalgorithm-merge-perl libasan4 libatomic1 libbinutils libcc1-0
      libcilkrts5 libdpkg-perl libexpat1-dev libfakeroot
      libfile-fcntllock-perl libgcc-7-dev libitm1 liblsan0 libmpx2
      libpython3-dev libpython3.6-dev libstdc++-7-dev libtsan0 libubsan0
      make python-pip-whl python3-crypto python3-dev python3-distutils
      python3-keyrings.alt python3-lib2to3 python3-setuptools
      python3-wheel python3-xdg python3.6-dev
    Suggested packages:
      binutils-doc debian-keyring g++-multilib g++-7-multilib gcc-7-doc
      libstdc++6-7-dbg gcc-multilib manpages-dev autoconf automake libtool
      flex bison gdb gcc-doc gcc-7-multilib gcc-7-locales libgcc1-dbg
      libgomp1-dbg libitm1-dbg libatomic1-dbg libasan4-dbg liblsan0-dbg
      libtsan0-dbg libubsan0-dbg libcilkrts5-dbg libmpx2-dbg
      libquadmath0-dbg git bzr libstdc++-7-doc make-doc python-crypto-doc
      gir1.2-gnomekeyring-1.0 python-setuptools-doc
    The following NEW packages will be installed:
      binutils binutils-common binutils-x86-64-linux-gnu build-essential
      dh-python dpkg-dev fakeroot g++ g++-7 gcc gcc-7
      libalgorithm-diff-perl libalgorithm-diff-xs-perl
      libalgorithm-merge-perl libasan4 libatomic1 libbinutils libcc1-0
      libcilkrts5 libdpkg-perl libexpat1-dev libfakeroot
      libfile-fcntllock-perl libgcc-7-dev libitm1 liblsan0 libmpx2
      libpython3-dev libpython3.6-dev libstdc++-7-dev libtsan0 libubsan0
      make python-pip-whl python3-crypto python3-dev python3-distutils
      python3-keyrings.alt python3-lib2to3 python3-pip python3-setuptools
      python3-wheel python3-xdg python3.6-dev
    0 upgraded, 44 newly installed, 0 to remove and 0 not upgraded.
    Need to get 75.8 MB of archives.
    After this operation, 196 MB of additional disk space will be used.

    $ ./key-mon
    (key-mon:1945): dbind-WARNING **: 07:09:21.956: Error retrieving
    accessibility bus address: org.freedesktop.DBus.Error.ServiceUnknown:
    The name org.a11y.Bus was not provided by any .service files
    Error: Missing xlib, run sudo apt-get install python-xlib
    $ sudo -H pip3 install xlib
    Collecting xlib
      Downloading https://files.pythonhosted.org/packages/3f/00/321541273b0ed2167b36c82be9baeb0bdc8af1c11c1b01de9436b84b5eaf/xlib-0.21-py2.py3-none-any.whl (123kB)
        100% |████████████████████████████████| 133kB 2.2MB/s
    Requirement already satisfied: six>=1.10.0 in
    /usr/lib/python3/dist-packages (from xlib)
    Installing collected packages: xlib
    Successfully installed xlib-0.21
    $ ./key-mon
    (key-mon:1945): dbind-WARNING **: 07:09:21.956: Error retrieving
    accessibility bus address: org.freedesktop.DBus.Error.ServiceUnknown:
    The name org.a11y.Bus was not provided by any .service files
```

...success... So... Lubuntu minimum needs LESS than Ubuntu Studio
20.04 with KDE.

```
    $ dpkg --get-selections | egrep -i "(xlib|cairo|gobject|mako)"
    libcairo-gobject-perl                           install
    libcairo-gobject2:amd64                         install
    libcairo-perl                                   install
    libcairo2:amd64                                 install
    libcairomm-1.0-1v5:amd64                        install
    liblightdm-gobject-1-0:amd64                    install
    libpangocairo-1.0-0:amd64                       install
    libpolkit-gobject-1-0:amd64                     install
    pxlib1                                          install
    python3-cairo:amd64                             install
    python3-gi-cairo                                install


    $ pip3 list | egrep -i "(xlib|cairo|gobject|mako)"
    pycairo                      1.16.2
    pygobject                    3.26.1
    xlib                         0.21
    $

    $ grep -h "import " * | sort | uniq
    from configparser import SafeConfigParser
    from gi.repository import Gtk
    from gi.repository import Gtk, Gdk, GLib
    from gi.repository import Gtk, GdkPixbuf
    from gi.repository import Gtk, GdkPixbuf, GObject, GLib
    from gi.repository import Gtk, GObject
      from . import key_mon
    from . import lazy_pixbuf_creator
    from . import mod_mapper
    from . import options
    from . import settings
    from . import shaped_window
    from . import two_state_image
      from . import xlib
    from Xlib.ext import record
    from Xlib import display
    from Xlib import X
    from Xlib import XK
    from Xlib.protocol import rq
    import cairo
    import codecs
    import collections
    import configparser
      #import cProfile
    import gettext
    import gi
      import io
    import io
    import locale
    import logging
    import optparse
    import os
    import re
    import subprocess
    import sys
    import tempfile
    import threading
    import time
    import types
    import unittest
```

----

## 2020.08.03 (KJC)

The format `%r` is a `repr()` function, which prints strings, at
least, with single quotes around the string. Like so:

```
    >>> x = "qwert"
    >>> print("the string %s is represented as %r" % (x, x))
    the string qwert is represented as 'qwert'
```

To adopt it to a format string:

```
    >>> x = "qwert"
    >>> print(f"the string {x} is represented as {x!r}")
    the string qwert is represented as 'qwert'
```

Apparently all this `print(_("Some string"))` is an abbreviation for
`print(gettext("Some string"))` and is used for translations into
other spoken / written languages. See the
[gettext](https://docs.python.org/3/library/gettext.html)
documentation.

----

## 2020.08.04 (KJC)

Today, three clever tricks: `git diff` after commit via `git show`,
`sed` and nested f-string formatting in Python.

### git show

To show the diffs of a particular commit: `git show <<commit hash>>`

### sed scripting

The file `sedate` reads:

```
    s/\\u00b4/\\N{Acute accent}/gI
    s/\\u00bf/\\N{Inverted question mark}/gI
    s/\\u2190/\\N{Leftwards arrow}/gI
    s/\\u2191/\\N{Upwards arrow}/gI
    s/\\u2192/\\N{Rightwards arrow}/gI
    s/\\u2193/\\N{Downwards arrow}/gI
    s/\\u21b9/\\N{Leftwards arrow to bar over rightwards arrow to bar}/gI
    s/\\u21d0/\\N{Leftwards double arrow}/gI
    s/\\u21d2/\\N{Rightwards double arrow}/gI
    s/\\u21fd/\\N{Leftwards open-headed arrow}/gI
    s/\\u23ce/\\N{Return symbol}/gI
```

which, as you've no doubt guessed, substitutes, globally, ignoring
case.  The double backslashes are needed to undo any interpretation of
it as an escape character. (On the command line, Bash needs **four**
backslashes.) To run the script:

```
    $ sed -i -f sedate *.py
```

### Nested f-string formatting in Python

The following took some decyphering as did its replacement:

```
    name_len = max(len(name) for name in theme_names)
    for theme in theme_names:
        print((' - %%-%ds: %%s' % name_len) % (theme, opts.themes[theme][0]))
```

What's happening there? Note it is nested. So, assuming for example,
that the longest string in the list of theme names is ten characters,
making `name_len` equal 10.

```
    (' - %%-%ds: %%s' % name_len) % (theme, opts.themes[theme][0])
```

After the inner format substitution takes place, the effective format
becomes:

```
    (' - %-10s: %s' % (theme, opts.themes[theme][0])
```

which pads the value of `theme` after the string with spaces to the
right for a total length of 10 characters. (Positive numbers pad
before the string, adding the spaces on the left.) A more intuitive
(?)  form uses an asterisk `*`, and the corresponding element of a
tuple to determine the padding.

```
    >>> length = 20
    >>> word = "qwert"
    >>> print("* %-*s '%s'" % (length, "x", word))
    * x                    'qwert'
```

To do the same thing with the newer **f-string** formatting is much
clearer:

```
    name_len = max(len(name) for name in theme_names)
    for theme in theme_names:
        print(f' - {theme:<{name_len}}: {opts.themes[theme][0]}')
```

(A greater than would do the padding before the string.)

### git

Well. It turns out that Codeberg isn't mirroring after all. Damn.

But there's apparently a fix:

```
    $ git remote set-url --add --push origin git@github.com:kjcole/key-mon.git
    $ git remote set-url --add --push origin git@codeberg.org:ubuntourist/key-mon.git
```

I believe that `git push` will now push to both Codeberg and GitHub
simultaneously.

But, keeping up with the goode Mr. Kirkwood's version requires the
additional:

```
    $ git remote add upstream git@github.com:scottkirkwood/key-mon.git
    $ git fetch upstream
    From github.com:scottkirkwood/key-mon
     * [new branch]      master     -> upstream/master
```

I think, that from now on, all I need to remember to do is:

```
    $ git pull upstream master
	From github.com:scottkirkwood/key-mon
     * branch            master     -> FETCH_HEAD
    Already up to date.
```

----

## 2021.04.22 (KJC)

After much fussing, I've merged what few changes from upstream exist.
And, a better install procedure.

```
$ python3 setup.py install --user
...
$ key-mon
Error: Missing xlib, run sudo apt-get install python-xlib
$ apt install python3-xlib
The following NEW packages will be installed:
  python3-xlib
0 upgraded, 1 newly installed, 0 to remove and 0 not upgraded.
Need to get 112 kB of archives.
After this operation, 776 kB of additional disk space will be used.
```

I forget what all the various "themes" look like, which one I
preferred, and how to set it as the default, but the basic code is
installed locally (`~/.local/lib/python3/...`) and working.

----
