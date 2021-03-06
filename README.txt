It can be used with SVN or BZR backends.

The SVN backend requires `pysvn` which you will probably want to install system-wide[1].

The BZR backend requires `bzrlib`. Currently only bzr 2.0+ is supported.

Basic usage::

    from sven.backend import SvnAccess
    client = SvnAccess(my_local_checkout_dir)
    
    client.write('path/to/a/file/to/write', "Lovely content to be versioning!")
    client.write('path/to/another/file', "Aw shucks, I'll version this too..",
                 msg="My commit message", mimetype='text/plain')

    last_rev_int = client.last_changed_rev('path/to/another/file')

    last_rev_int = last_rev_int - 1
    from sven.exc import ResourceUnchanged
    try:
        earlier_version = client.read('path/to/another/file', rev=last_rev_int)
    except ResourceUnchanged, exc:
    	last_rev_int = exc.last_change
        earlier_version = client.read('path/to/another/file', rev=last_rev_int)
	
    changelog = client.log('path/to/another/file', rev=last_rev_int)

To use the BZR backend,

    from sven.bzr import BzrAccess

The interface is the same.

Each `.write` writes the content to the path on the local filesystem's checkout
and then commits it to the repository. The workflow of one-write-per-commit is
by design and is not likely to change soon; if you need a different workflow,
you probably ought to just be using svn clients directly, anyway.

Currently sven does not help you set up a repository client or server.  It 
assumes you've already got a repository and checkout (which may be the same
thing, in the BZR case) set up.

The formats returned by some of its methods (.read, .log and .ls) are
totally ad-hoc right now and strange; they'll probably be formalized sooner
or later.

For more detailed usage documentation please see ./sven/doctest.txt (which
can be run as a test suite by `python sven/backend.py`) and ./sven/bzr.txt
(which can be run as a test suite by `python sven/bzr.py`)

[1] If you start to experience Segmentation faults while using sven, especially
    during .write operations, your versions of ``svn`` and ``pysvn`` are likely
    incompatible (e.g. svn 1.5 with pysvn compiled against your earlier svn 1.4)
    If this happens, you should uninstall pysvn, then compile it from source.
    You might want to test for this upfront by running the test suite:

     python sven/backend.py

Follow sven on github: <http://github.com/socialplanning/sven>
