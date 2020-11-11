# test case for nektos/act

This is using:
```
wink@3900x:~/prgs/nektos/forks/act (master)
$ git log --pretty=oneline -1
695c4966843d319933d110316032863d138a721b (HEAD -> master, upstream/master, origin/master, origin/HEAD) Adds ability to use container images from matrices. (#413)
```

A test case showing the python-cache step fails
because `env.pythonLocation` is empty:
```
86	[t1/Linux build and test]   ❗  ::error::Input required and not supplied: path
87	| (node:68) [DEP0091] DeprecationWarning: crypto.DEFAULT_ENCODING is deprecated.
88	| (node:68) [DEP0010] DeprecationWarning: crypto.createCredentials is deprecated. Use tls.createSecureContext instead.
89	| (node:68) [DEP0011] DeprecationWarning: crypto.Credentials is deprecated. Use tls.SecureContext instead.
90	[t1/Linux build and test]   ❌  Failure - Python cache
91	Error: exit with `FAILURE`: 1
```
But notice at line 74 we see `pythonLocation` being set:
```
74	[t1/Linux build and test]   ⚙  ::set-env:: pythonLocation=/opt/hostedtoolcache/Python/3.6.12/x64
```

Here is the full log:
```
 1	[t1/Linux build and test] 🧪  Matrix: map[python-version:3.6]
 2	[t1/Linux build and test] 🚀  Start image=winksaville/ubuntu-dev:20.04
 3	[t1/Linux build and test]   🐳  docker run image=winksaville/ubuntu-dev:20.04 entrypoint=["/usr/bin/tail" "-f" "/dev/null"] cmd=[]
 4	[t1/Linux build and test]   🐳  docker cp src=/home/wink/prgs/python/projects/hw/. dst=/github/workspace
 5	[t1/Linux build and test] ⭐  Run actions/checkout@v2
 6	[t1/Linux build and test]   ✅  Success - actions/checkout@v2
 7	[t1/Linux build and test] ⭐  Run Set up Python ${{ matrix.python-version }}
 8	[t1/Linux build and test]   ☁  git clone 'https://github.com/actions/setup-python' # ref=v2
 9	[t1/Linux build and test]   🐳  docker cp src=/home/wink/.cache/act/actions-setup-python@v2 dst=/actions/
10	[t1/Linux build and test]   💬  ::debug::Semantic version spec of 3.6 is 3.6
11	[t1/Linux build and test]   💬  ::debug::isExplicit: 
12	[t1/Linux build and test]   💬  ::debug::explicit? false
13	[t1/Linux build and test]   💬  ::debug::evaluating 0 versions
14	[t1/Linux build and test]   💬  ::debug::match not found
15	| Version 3.6 was not found in the local cache
16	[t1/Linux build and test]   💬  ::debug::check 3.10.0-alpha.2 satisfies 3.6
17	[t1/Linux build and test]   💬  ::debug::check 3.10.0-alpha.1 satisfies 3.6
18	[t1/Linux build and test]   💬  ::debug::check 3.9.0 satisfies 3.6
19	[t1/Linux build and test]   💬  ::debug::check 3.9.0-rc.2 satisfies 3.6
20	[t1/Linux build and test]   💬  ::debug::check 3.9.0-rc.1 satisfies 3.6
21	[t1/Linux build and test]   💬  ::debug::check 3.9.0-beta.5 satisfies 3.6
22	[t1/Linux build and test]   💬  ::debug::check 3.9.0-beta.4 satisfies 3.6
23	[t1/Linux build and test]   💬  ::debug::check 3.8.6 satisfies 3.6
24	[t1/Linux build and test]   💬  ::debug::check 3.8.5 satisfies 3.6
25	[t1/Linux build and test]   💬  ::debug::check 3.8.4 satisfies 3.6
26	[t1/Linux build and test]   💬  ::debug::check 3.8.3 satisfies 3.6
27	[t1/Linux build and test]   💬  ::debug::check 3.8.2 satisfies 3.6
28	[t1/Linux build and test]   💬  ::debug::check 3.8.1 satisfies 3.6
29	[t1/Linux build and test]   💬  ::debug::check 3.8.0 satisfies 3.6
30	[t1/Linux build and test]   💬  ::debug::check 3.7.9 satisfies 3.6
31	[t1/Linux build and test]   💬  ::debug::check 3.7.8 satisfies 3.6
32	[t1/Linux build and test]   💬  ::debug::check 3.7.7 satisfies 3.6
33	[t1/Linux build and test]   💬  ::debug::check 3.7.6 satisfies 3.6
34	[t1/Linux build and test]   💬  ::debug::check 3.7.5 satisfies 3.6
35	[t1/Linux build and test]   💬  ::debug::check 3.6.12 satisfies 3.6
36	[t1/Linux build and test]   💬  ::debug::x64===x64 && darwin===linux
37	[t1/Linux build and test]   💬  ::debug::x64===x64 && linux===linux
38	[t1/Linux build and test]   💬  ::debug::x64===x64 && linux===linux
39	[t1/Linux build and test]   💬  ::debug::x64===x64 && linux===linux
40	[t1/Linux build and test]   💬  ::debug::matched 3.6.12
41	| Version 3.6 is available for downloading
42	| Download from "https://github.com/actions/python-versions/releases/download/3.6.12-86954/python-3.6.12-linux-20.04-x64.tar.gz"
43	[t1/Linux build and test]   💬  ::debug::Downloading https://github.com/actions/python-versions/releases/download/3.6.12-86954/python-3.6.12-linux-20.04-x64.tar.gz
44	[t1/Linux build and test]   💬  ::debug::Destination /tmp/d87d2135-b534-49ff-ba99-b4af94ccd6d1
45	[t1/Linux build and test]   💬  ::debug::download complete
46	| Extract downloaded archive
47	[t1/Linux build and test]   💬  ::debug::Checking tar --version
48	[t1/Linux build and test]   💬  ::debug::tar (GNU tar) 1.30%0ACopyright (C) 2017 Free Software Foundation, Inc.%0ALicense GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>.%0AThis is free software: you are free to change and redistribute it.%0AThere is NO WARRANTY, to the extent permitted by law.%0A%0AWritten by John Gilmore and Jay Fenlason.
49	| [command]/usr/bin/tar xz --warning=no-unknown-keyword -C /tmp/d083bbab-db90-40a8-925b-ac91b0a8fd07 -f /tmp/d87d2135-b534-49ff-ba99-b4af94ccd6d1
50	| Execute installation script
51	| Check if Python hostedtoolcache folder exist...
52	| Creating Python hostedtoolcache folder...
53	| Create Python 3.6.12 folder
54	| Copy Python binaries to hostedtoolcache folder
55	| Create additional symlinks (Required for the UsePythonVersion Azure Pipelines task and the setup-python GitHub Action)
56	| Upgrading PIP...
57	| Looking in links: /tmp/tmpf699kgxt
58	| Requirement already satisfied: setuptools in /opt/hostedtoolcache/Python/3.6.12/x64/lib/python3.6/site-packages (40.6.2)
59	| Requirement already satisfied: pip in /opt/hostedtoolcache/Python/3.6.12/x64/lib/python3.6/site-packages (18.1)
60	| Collecting pip
61	| Downloading https://files.pythonhosted.org/packages/cb/28/91f26bd088ce8e22169032100d4260614fc3da435025ff389ef1d396a433/pip-20.2.4-py2.py3-none-any.whl (1.5MB)
62	| Installing collected packages: pip
63	| Successfully installed pip-20.2.4
64	[t1/Linux build and test]   ❗  ::error::You are using pip version 20.2.4, however version 20.3b1 is available.%0AYou should consider upgrading via the 'pip install --upgrade pip' command.
65	| Create complete file
66	[t1/Linux build and test]   💬  ::debug::isExplicit: 
67	[t1/Linux build and test]   💬  ::debug::explicit? false
68	[t1/Linux build and test]   💬  ::debug::isExplicit: 3.6.12
69	[t1/Linux build and test]   💬  ::debug::explicit? true
70	[t1/Linux build and test]   💬  ::debug::evaluating 1 versions
71	[t1/Linux build and test]   💬  ::debug::matched: 3.6.12
72	[t1/Linux build and test]   💬  ::debug::checking cache: /opt/hostedtoolcache/Python/3.6.12/x64
73	[t1/Linux build and test]   💬  ::debug::Found tool in cache Python 3.6.12 x64
74	[t1/Linux build and test]   ⚙  ::set-env:: pythonLocation=/opt/hostedtoolcache/Python/3.6.12/x64
75	[t1/Linux build and test]   ⚙  ::set-env:: LD_LIBRARY_PATH=/opt/hostedtoolcache/Python/3.6.12/x64/lib
76	[t1/Linux build and test]   ⚙  ::add-path:: /opt/hostedtoolcache/Python/3.6.12/x64
77	[t1/Linux build and test]   ⚙  ::add-path:: /opt/hostedtoolcache/Python/3.6.12/x64/bin
78	[t1/Linux build and test]   ⚙  ::set-output:: python-version=3.6.12
79	| Successfully setup CPython (3.6.12)
80	[t1/Linux build and test]   ❓  ##[add-matcher]/actions/actions-setup-python@v2/.github/python.json
81	[t1/Linux build and test]   ✅  Success - Set up Python ${{ matrix.python-version }}
82	[t1/Linux build and test] ⭐  Run Python cache
83	[t1/Linux build and test]   ☁  git clone 'https://github.com/actions/cache' # ref=v2
84	[t1/Linux build and test]   🐳  docker cp src=/home/wink/.cache/act/actions-cache@v2 dst=/actions/
85	[t1/Linux build and test]   ❓  ::save-state name=CACHE_KEY::Linux-python--e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
86	[t1/Linux build and test]   ❗  ::error::Input required and not supplied: path
87	| (node:68) [DEP0091] DeprecationWarning: crypto.DEFAULT_ENCODING is deprecated.
88	| (node:68) [DEP0010] DeprecationWarning: crypto.createCredentials is deprecated. Use tls.createSecureContext instead.
89	| (node:68) [DEP0011] DeprecationWarning: crypto.Credentials is deprecated. Use tls.SecureContext instead.
90	[t1/Linux build and test]   ❌  Failure - Python cache
91	Error: exit with `FAILURE`: 1
```

But if I add a step that displays `env.pythonLocation` before `python-cache`:
```
      - name: Display env.pythonLocation
        run: |
          echo env.pythonLocation=${{ env.pythonLocation }}
```

It does complete all of the steps:
```
  1	$ ~/prgs/nektos/forks/act/dist/local/act
  2	[t1/Linux build and test] 🧪  Matrix: map[python-version:3.6]
  3	[t1/Linux build and test] 🚀  Start image=winksaville/ubuntu-dev:20.04
  4	[t1/Linux build and test]   🐳  docker run image=winksaville/ubuntu-dev:20.04 entrypoint=["/usr/bin/tail" "-f" "/dev/null"] cmd=[]
  5	[t1/Linux build and test]   🐳  docker cp src=/home/wink/prgs/python/projects/hw/. dst=/github/workspace
  6	[t1/Linux build and test] ⭐  Run actions/checkout@v2
  7	[t1/Linux build and test]   ✅  Success - actions/checkout@v2
  8	[t1/Linux build and test] ⭐  Run Set up Python ${{ matrix.python-version }}
  9	[t1/Linux build and test]   ☁  git clone 'https://github.com/actions/setup-python' # ref=v2
 10	[t1/Linux build and test]   🐳  docker cp src=/home/wink/.cache/act/actions-setup-python@v2 dst=/actions/
 11	[t1/Linux build and test]   💬  ::debug::Semantic version spec of 3.6 is 3.6
 12	[t1/Linux build and test]   💬  ::debug::isExplicit: 
 13	[t1/Linux build and test]   💬  ::debug::explicit? false
 14	[t1/Linux build and test]   💬  ::debug::evaluating 0 versions
 15	[t1/Linux build and test]   💬  ::debug::match not found
 16	| Version 3.6 was not found in the local cache
 17	[t1/Linux build and test]   💬  ::debug::check 3.10.0-alpha.2 satisfies 3.6
 18	[t1/Linux build and test]   💬  ::debug::check 3.10.0-alpha.1 satisfies 3.6
 19	[t1/Linux build and test]   💬  ::debug::check 3.9.0 satisfies 3.6
 20	[t1/Linux build and test]   💬  ::debug::check 3.9.0-rc.2 satisfies 3.6
 21	[t1/Linux build and test]   💬  ::debug::check 3.9.0-rc.1 satisfies 3.6
 22	[t1/Linux build and test]   💬  ::debug::check 3.9.0-beta.5 satisfies 3.6
 23	[t1/Linux build and test]   💬  ::debug::check 3.9.0-beta.4 satisfies 3.6
 24	[t1/Linux build and test]   💬  ::debug::check 3.8.6 satisfies 3.6
 25	[t1/Linux build and test]   💬  ::debug::check 3.8.5 satisfies 3.6
 26	[t1/Linux build and test]   💬  ::debug::check 3.8.4 satisfies 3.6
 27	[t1/Linux build and test]   💬  ::debug::check 3.8.3 satisfies 3.6
 28	[t1/Linux build and test]   💬  ::debug::check 3.8.2 satisfies 3.6
 29	[t1/Linux build and test]   💬  ::debug::check 3.8.1 satisfies 3.6
 30	[t1/Linux build and test]   💬  ::debug::check 3.8.0 satisfies 3.6
 31	[t1/Linux build and test]   💬  ::debug::check 3.7.9 satisfies 3.6
 32	[t1/Linux build and test]   💬  ::debug::check 3.7.8 satisfies 3.6
 33	[t1/Linux build and test]   💬  ::debug::check 3.7.7 satisfies 3.6
 34	[t1/Linux build and test]   💬  ::debug::check 3.7.6 satisfies 3.6
 35	[t1/Linux build and test]   💬  ::debug::check 3.7.5 satisfies 3.6
 36	[t1/Linux build and test]   💬  ::debug::check 3.6.12 satisfies 3.6
 37	[t1/Linux build and test]   💬  ::debug::x64===x64 && darwin===linux
 38	[t1/Linux build and test]   💬  ::debug::x64===x64 && linux===linux
 39	[t1/Linux build and test]   💬  ::debug::x64===x64 && linux===linux
 40	[t1/Linux build and test]   💬  ::debug::x64===x64 && linux===linux
 41	[t1/Linux build and test]   💬  ::debug::matched 3.6.12
 42	| Version 3.6 is available for downloading
 43	| Download from "https://github.com/actions/python-versions/releases/download/3.6.12-86954/python-3.6.12-linux-20.04-x64.tar.gz"
 44	[t1/Linux build and test]   💬  ::debug::Downloading https://github.com/actions/python-versions/releases/download/3.6.12-86954/python-3.6.12-linux-20.04-x64.tar.gz
 45	[t1/Linux build and test]   💬  ::debug::Destination /tmp/c495d26e-cc71-42d0-9493-19511dab5bd0
 46	[t1/Linux build and test]   💬  ::debug::download complete
 47	| Extract downloaded archive
 48	[t1/Linux build and test]   💬  ::debug::Checking tar --version
 49	[t1/Linux build and test]   💬  ::debug::tar (GNU tar) 1.30%0ACopyright (C) 2017 Free Software Foundation, Inc.%0ALicense GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>.%0AThis is free software: you are free to change and redistribute it.%0AThere is NO WARRANTY, to the extent permitted by law.%0A%0AWritten by John Gilmore and Jay Fenlason.
 50	| [command]/usr/bin/tar xz --warning=no-unknown-keyword -C /tmp/29f48a50-fedc-4db0-bb3e-5c181150eacf -f /tmp/c495d26e-cc71-42d0-9493-19511dab5bd0
 51	| Execute installation script
 52	| Check if Python hostedtoolcache folder exist...
 53	| Creating Python hostedtoolcache folder...
 54	| Create Python 3.6.12 folder
 55	| Copy Python binaries to hostedtoolcache folder
 56	| Create additional symlinks (Required for the UsePythonVersion Azure Pipelines task and the setup-python GitHub Action)
 57	| Upgrading PIP...
 58	| Looking in links: /tmp/tmp5bged5zx
 59	| Requirement already satisfied: setuptools in /opt/hostedtoolcache/Python/3.6.12/x64/lib/python3.6/site-packages (40.6.2)
 60	| Requirement already satisfied: pip in /opt/hostedtoolcache/Python/3.6.12/x64/lib/python3.6/site-packages (18.1)
 61	| Collecting pip
 62	| Downloading https://files.pythonhosted.org/packages/cb/28/91f26bd088ce8e22169032100d4260614fc3da435025ff389ef1d396a433/pip-20.2.4-py2.py3-none-any.whl (1.5MB)
 63	| Installing collected packages: pip
 64	| Successfully installed pip-20.2.4
 65	[t1/Linux build and test]   ❗  ::error::You are using pip version 20.2.4, however version 20.3b1 is available.%0AYou should consider upgrading via the 'pip install --upgrade pip' command.
 66	| Create complete file
 67	[t1/Linux build and test]   💬  ::debug::isExplicit: 
 68	[t1/Linux build and test]   💬  ::debug::explicit? false
 69	[t1/Linux build and test]   💬  ::debug::isExplicit: 3.6.12
 70	[t1/Linux build and test]   💬  ::debug::explicit? true
 71	[t1/Linux build and test]   💬  ::debug::evaluating 1 versions
 72	[t1/Linux build and test]   💬  ::debug::matched: 3.6.12
 73	[t1/Linux build and test]   💬  ::debug::checking cache: /opt/hostedtoolcache/Python/3.6.12/x64
 74	[t1/Linux build and test]   💬  ::debug::Found tool in cache Python 3.6.12 x64
 75	[t1/Linux build and test]   ⚙  ::set-env:: pythonLocation=/opt/hostedtoolcache/Python/3.6.12/x64
 76	[t1/Linux build and test]   ⚙  ::set-env:: LD_LIBRARY_PATH=/opt/hostedtoolcache/Python/3.6.12/x64/lib
 77	[t1/Linux build and test]   ⚙  ::add-path:: /opt/hostedtoolcache/Python/3.6.12/x64
 78	[t1/Linux build and test]   ⚙  ::add-path:: /opt/hostedtoolcache/Python/3.6.12/x64/bin
 79	[t1/Linux build and test]   ⚙  ::set-output:: python-version=3.6.12
 80	| Successfully setup CPython (3.6.12)
 81	[t1/Linux build and test]   ❓  ##[add-matcher]/actions/actions-setup-python@v2/.github/python.json
 82	[t1/Linux build and test]   ✅  Success - Set up Python ${{ matrix.python-version }}
 83	[t1/Linux build and test] ⭐  Run Display env.pythonLocation
 84	| env.pythonLocation=/opt/hostedtoolcache/Python/3.6.12/x64
 85	[t1/Linux build and test]   ✅  Success - Display env.pythonLocation
 86	[t1/Linux build and test] ⭐  Run Python cache
 87	[t1/Linux build and test]   ☁  git clone 'https://github.com/actions/cache' # ref=v2
 88	[t1/Linux build and test]   🐳  docker cp src=/home/wink/.cache/act/actions-cache@v2 dst=/actions/
 89	[t1/Linux build and test]   ❓  ::save-state name=CACHE_KEY::Linux-python--e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
 90	[t1/Linux build and test]   💬  ::debug::Resolved Keys:
 91	[t1/Linux build and test]   💬  ::debug::["Linux-python--e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855","Linux-python-/opt/hostedtoolcache/Python/3.6.12/x64"]
 92	[t1/Linux build and test]   💬  ::debug::Checking zstd --version
 93	| (node:72) [DEP0091] DeprecationWarning: crypto.DEFAULT_ENCODING is deprecated.
 94	| (node:72) [DEP0010] DeprecationWarning: crypto.createCredentials is deprecated. Use tls.createSecureContext instead.
 95	| (node:72) [DEP0011] DeprecationWarning: crypto.Credentials is deprecated. Use tls.SecureContext instead.
 96	[t1/Linux build and test]   💬  ::debug::Unable to locate executable file: zstd. Please verify either the file path exists or the file can be found within a directory specified by the PATH environment variable. Also check the file mode to verify the file is executable.
 97	[t1/Linux build and test]   💬  ::debug::
 98	[t1/Linux build and test]   💬  ::debug::getCacheEntry - Attempt 1 of 2 failed with error: Cache Service Url not found, unable to restore cache.
 99	[t1/Linux build and test]   💬  ::debug::getCacheEntry - Attempt 2 of 2 failed with error: Cache Service Url not found, unable to restore cache.
100	| [warning]getCacheEntry failed: Cache Service Url not found, unable to restore cache.
101	[t1/Linux build and test]   ⚙  ::set-output:: cache-hit=false
102	[t1/Linux build and test]   ✅  Success - Python cache
103	[t1/Linux build and test] ⭐  Run Test if cache hit is not true
104	| steps.python-cache.outputs.cache-hit is NOT true
105	[t1/Linux build and test]   ✅  Success - Test if cache hit is not true
106	[t1/Linux build and test] ⭐  Run Always
107	| Done
108	[t1/Linux build and test]   ✅  Success - Always
109	[t1/Linux build and test] ⭐  Run Done
110	| Done
111	[t1/Linux build and test]   ✅  Success - Done
```