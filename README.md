# About this environment

This environment includes **Pandas** and such to support the Python based machine learning courses.

## The course

Course notebooks, code and documentation are to tracked in their own repository. To simplify reusability (and because of the way `Direnv` works, which doesn't sit well with Dropbox and sharing the environment across multiple computers with different versions of macOS), the environment is not shared (it is synchronized via GIT), while the course can be if so wanted..

The course folder is linked as `course`. This is a recommendation, obviously, and you can do as you please.

## Direnv

When using `direnv`, the virtualenv environment is setup and activated upon accessing the directory.

`pip install -r requirements.txt`

If the containing folder gets moved, it is better to throw away (delete) the `.direnv` folder as some paths are configured statically during the automated setup.

## Notes

### To find out which Python kernels are available in `Jupyter`

`jupyter kernelspec list`

### To execute `Jupyter` notebooks non-interactively (and generated documents)

* To execute: `--ExecutePreprocessor.enabled=True`
, or just `--execute`.
* To strip outputs: `--ClearOutputPreprocessor.enabled=True`
* To allow errors: `--allow-errors`
* To generate a PDF: `--to=pdf`. This requires `pandoc` and `mactex` (for instance). Also, the `pdftex` or `xelatex` binaries need to be in the `PATH`. For mactex installed via Homebrew that's `/Library/TeX/texbin`.

An example of the excution would be:

```
jupyter nbconvert --to=pdf --execute <notebook>
```

### To set a matplotlib backend for non-GUI python shells

This would be needed, for instance, on macOS.

```
echo "backend: TkAgg" >> ~/.matplotlib/matplotlibrc
```

### Extensions

After installing the contributed notebook extensions (from `jupyter_contrib_nbextensions`), they need to be copied to the system:

`jupyter contrib nbextension install --sys-prefix`

With this they will be available via the internal extension configuration tab. Or use the command line:

```
jupyter nbextension enable hide_header/main
jupyter nbextension enable freeze/main
```

## Issues

### Possible with `python3`

When using a `python3` installed via `brew install` it might happen that there are already `/usr/local/bin/python3` links to previous installations (which doesn't work as the `python3` from Homebrew requires a System or a Homebrew set of binaries, and installations under `/Library` might fail). If you have issues with this, execute `brew doctor`.

A way in which this issue might present itself is via this type of error messages:

```
Fatal Python error: Py_Initialize: unable to load the file system codec
LookupError: no codec search functions registered: can't find encoding

Current thread 0x0000000100f85310 (most recent call first):
```