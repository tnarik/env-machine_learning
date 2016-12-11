# About this environment

This environment includes **Pandas** and such to support the Python based machine learning courses.

## The course

The course notebooks, code and documentation is tracked in its own repository. To simplify reusability (and because of the way Direnv works, which doesn't sit well with Dropbox and sharing the environment across multiple computers with different versions of macOS), the course is shared, the environment is not (it is synchronized via GIT).

The course folder is linked as `course`. This is a recommendation, obviously, and you can do as you please.

## Direnv

When using `direnv`, the virtualenv environment is setup and activated upon accessing the directory.

`pip install -r requirements.txt`


## Notes

When using a `python3` installed via `brew installz it might happen that there are already `/usr/local/bin/python3` links to previous installations (which doesn't work as the `python3` from Homebrew requires a System or a Homebrew set of binaries, and installations under `/Library` might fail). If you have issues with this, execute `brew doctor`.

A way in which this issue might present itself is via this type or error messages:

```
Fatal Python error: Py_Initialize: unable to load the file system codec
LookupError: no codec search functions registered: can't find encoding

Current thread 0x0000000100f85310 (most recent call first):
```