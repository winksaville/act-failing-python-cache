# test case for nektos/act

This is using:
```
wink@3900x:~/prgs/nektos/forks/act (master)
$ git log --pretty=oneline -1
695c4966843d319933d110316032863d138a721b (HEAD -> master, upstream/master, origin/master, origin/HEAD) Adds ability to use container images from matrices. (#413)
```

A test case showing the python-cache step fails
because env.pythonLocation cannot be used to set the path
when using act:
```
wink@3900x:~/prgs/python/projects/hw (main)
$ ~/prgs/nektos/forks/act/dist/local/act
[t1/Linux build and test] 🧪  Matrix: map[python-version:3.6]
[t1/Linux build and test] 🚀  Start image=winksaville/ubuntu-dev:20.04
[t1/Linux build and test]   🐳  docker run image=winksaville/ubuntu-dev:20.04 entrypoint=["/usr/bin/tail" "-f" "/dev/null"] cmd=[]
[t1/Linux build and test]   🐳  docker cp src=/home/wink/prgs/python/projects/hw/. dst=/github/workspace
[t1/Linux build and test] ⭐  Run actions/checkout@v2
[t1/Linux build and test]   ✅  Success - actions/checkout@v2
[t1/Linux build and test] ⭐  Run Set up Python ${{ matrix.python-version }}
[t1/Linux build and test]   ☁  git clone 'https://github.com/actions/setup-python' # ref=v2
[t1/Linux build and test]   🐳  docker cp src=/home/wink/.cache/act/actions-setup-python@v2 dst=/actions/
[t1/Linux build and test]   💬  ::debug::Semantic version spec of 3.6 is 3.6
[t1/Linux build and test]   💬  ::debug::isExplicit: 
[t1/Linux build and test]   💬  ::debug::explicit? false
[t1/Linux build and test]   💬  ::debug::evaluating 0 versions
[t1/Linux build and test]   💬  ::debug::match not found
| Version 3.6 was not found in the local cache
[t1/Linux build and test]   💬  ::debug::check 3.10.0-alpha.2 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.10.0-alpha.1 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.9.0 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.9.0-rc.2 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.9.0-rc.1 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.9.0-beta.5 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.9.0-beta.4 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.8.6 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.8.5 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.8.4 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.8.3 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.8.2 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.8.1 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.8.0 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.7.9 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.7.8 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.7.7 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.7.6 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.7.5 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::check 3.6.12 satisfies 3.6
[t1/Linux build and test]   💬  ::debug::x64===x64 && darwin===linux
[t1/Linux build and test]   💬  ::debug::x64===x64 && linux===linux
[t1/Linux build and test]   💬  ::debug::x64===x64 && linux===linux
[t1/Linux build and test]   💬  ::debug::x64===x64 && linux===linux
[t1/Linux build and test]   💬  ::debug::matched 3.6.12
| Version 3.6 is available for downloading
| Download from "https://github.com/actions/python-versions/releases/download/3.6.12-86954/python-3.6.12-linux-20.04-x64.tar.gz"
[t1/Linux build and test]   💬  ::debug::Downloading https://github.com/actions/python-versions/releases/download/3.6.12-86954/python-3.6.12-linux-20.04-x64.tar.gz
[t1/Linux build and test]   💬  ::debug::Destination /tmp/749129b8-2c15-448c-bec5-7c690c6c3890
[t1/Linux build and test]   💬  ::debug::download complete
| Extract downloaded archive
[t1/Linux build and test]   💬  ::debug::Checking tar --version
[t1/Linux build and test]   💬  ::debug::tar (GNU tar) 1.30%0ACopyright (C) 2017 Free Software Foundation, Inc.%0ALicense GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>.%0AThis is free software: you are free to change and redistribute it.%0AThere is NO WARRANTY, to the extent permitted by law.%0A%0AWritten by John Gilmore and Jay Fenlason.
| [command]/usr/bin/tar xz --warning=no-unknown-keyword -C /tmp/01b5181b-0810-45f1-846f-145dafe6570f -f /tmp/749129b8-2c15-448c-bec5-7c690c6c3890
| Execute installation script
| Check if Python hostedtoolcache folder exist...
| Creating Python hostedtoolcache folder...
| Create Python 3.6.12 folder
| Copy Python binaries to hostedtoolcache folder
| Create additional symlinks (Required for the UsePythonVersion Azure Pipelines task and the setup-python GitHub Action)
| Upgrading PIP...
| Looking in links: /tmp/tmp1sy_bm5m
| Requirement already satisfied: setuptools in /opt/hostedtoolcache/Python/3.6.12/x64/lib/python3.6/site-packages (40.6.2)
| Requirement already satisfied: pip in /opt/hostedtoolcache/Python/3.6.12/x64/lib/python3.6/site-packages (18.1)
| Collecting pip
| Downloading https://files.pythonhosted.org/packages/cb/28/91f26bd088ce8e22169032100d4260614fc3da435025ff389ef1d396a433/pip-20.2.4-py2.py3-none-any.whl (1.5MB)
| Installing collected packages: pip
| Successfully installed pip-20.2.4
[t1/Linux build and test]   ❗  ::error::You are using pip version 20.2.4, however version 20.3b1 is available.%0AYou should consider upgrading via the 'pip install --upgrade pip' command.
| Create complete file
[t1/Linux build and test]   💬  ::debug::isExplicit: 
[t1/Linux build and test]   💬  ::debug::explicit? false
[t1/Linux build and test]   💬  ::debug::isExplicit: 3.6.12
[t1/Linux build and test]   💬  ::debug::explicit? true
[t1/Linux build and test]   💬  ::debug::evaluating 1 versions
[t1/Linux build and test]   💬  ::debug::matched: 3.6.12
[t1/Linux build and test]   💬  ::debug::checking cache: /opt/hostedtoolcache/Python/3.6.12/x64
[t1/Linux build and test]   💬  ::debug::Found tool in cache Python 3.6.12 x64
[t1/Linux build and test]   ⚙  ::set-env:: pythonLocation=/opt/hostedtoolcache/Python/3.6.12/x64
[t1/Linux build and test]   ⚙  ::set-env:: LD_LIBRARY_PATH=/opt/hostedtoolcache/Python/3.6.12/x64/lib
[t1/Linux build and test]   ⚙  ::add-path:: /opt/hostedtoolcache/Python/3.6.12/x64
[t1/Linux build and test]   ⚙  ::add-path:: /opt/hostedtoolcache/Python/3.6.12/x64/bin
[t1/Linux build and test]   ⚙  ::set-output:: python-version=3.6.12
| Successfully setup CPython (3.6.12)
[t1/Linux build and test]   ❓  ##[add-matcher]/actions/actions-setup-python@v2/.github/python.json
[t1/Linux build and test]   ✅  Success - Set up Python ${{ matrix.python-version }}
[t1/Linux build and test] ⭐  Run Python cache
[t1/Linux build and test]   ☁  git clone 'https://github.com/actions/cache' # ref=v2
[t1/Linux build and test]   🐳  docker cp src=/home/wink/.cache/act/actions-cache@v2 dst=/actions/
[t1/Linux build and test]   ❓  ::save-state name=CACHE_KEY::Linux-python--e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
[t1/Linux build and test]   ❗  ::error::Input required and not supplied: path
| (node:67) [DEP0091] DeprecationWarning: crypto.DEFAULT_ENCODING is deprecated.
| (node:67) [DEP0010] DeprecationWarning: crypto.createCredentials is deprecated. Use tls.createSecureContext instead.
| (node:67) [DEP0011] DeprecationWarning: crypto.Credentials is deprecated. Use tls.SecureContext instead.
[t1/Linux build and test]   ❌  Failure - Python cache
Error: exit with `FAILURE`: 1
[3]+  Done                    git gui
```
