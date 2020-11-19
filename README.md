# nektos/act handling of env variables

This is using my fork of [act with added debug](https://github.com/winksaville/act/tree/debug-act-failing-python-cache):
```
wink@3900x:~/prgs/nektos/forks/act (debug-act-failing-python-cache)
$ git log --pretty=oneline -1
a5fc66fbd3843e05581f9b3c851735d4337f2a5f (HEAD -> debug-act-failing-python-cache) Add more debug
```

Here is the python_cache.yml:
```
name: python_cache
on: push

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - name: step1
        uses: actions/setup-python@v2
        with:
          python-version: 3.7

      - name: step2
        uses: actions/cache@v2
        with:
          path: ${{ env.pythonLocation }}
          key: ${{ runner.os }}-python-${{ env.pythonLocation }}

      - name: Done
        run:  echo Done
```

And here is the failure where the `path` parameter in step2 is empty
and it reports `::error::Input required and not supplied: path`.
```
238	[python_cache/test]   ❗  ::error::Input required and not supplied: path
239	[python_cache/test]   | (node:62) [DEP0091] DeprecationWarning: crypto.DEFAULT_ENCODING is deprecated.
240	[python_cache/test]   | (node:62) [DEP0010] DeprecationWarning: crypto.createCredentials is deprecated. Use tls.createSecureContext instead.
241	[python_cache/test]   | (node:62) [DEP0011] DeprecationWarning: crypto.Credentials is deprecated. Use tls.SecureContext instead.
242	[python_cache/test]   ❌  Failure - step2
243	time="2020-11-19T14:23:05-08:00" level=debug msg="WINK: newStepExecutor.func:- Run sc=&{RunContext:python_cache/test Step:step2 Env:map[ACT:true CI:true GITHUB_ACTION:1 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_KEY:Linux-python- INPUT_PATH: INPUT_RESTORE-KEYS: INPUT_UPLOAD-CHUNK-SIZE: LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib PATH:/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64] Cmd:[] Action:0xc0004196c0} rc.StepResults=&{Success:false Outputs:map[]} rc.GetEnv=map[ACT:true LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64]"
244	time="2020-11-19T14:23:05-08:00" level=debug msg="exit with `FAILURE`: 1"
245	time="2020-11-19T14:23:05-08:00" level=debug msg="exit with `FAILURE`: 1"
246	Error: exit with `FAILURE`: 1
```

But notice at line 157 `pythonLocation` is being set:
```
157	[python_cache/test]   ⚙  ::set-env:: pythonLocation=/opt/hostedtoolcache/Python/3.7.9/x64
```

And the reson it fails is because at line 218 in the full log `INPUT_PATH` is empty, it should
be `/opt/hostedtoolcache/Python/3.7.9/x64`. And `INPUT_KEY` is `Linux-python-`
where as it should be `Linux-python-/opt/hostedtoolcache/Python/3.7.9/x64`
```
INPUT_KEY:Linux-python- INPUT_PATH:
```

Here is the full log:
```
wink@3900x:~/prgs/python/projects/act-failing-python-cache (main)
$ cat -n act-ga5fc66f.log 
     1	time="2020-11-19T14:22:52-08:00" level=debug msg="Loading environment from /home/wink/prgs/python/projects/act-failing-python-cache/.env"
     2	time="2020-11-19T14:22:52-08:00" level=debug msg="Loading secrets from "
     3	time="2020-11-19T14:22:52-08:00" level=debug msg="Loading workflows from '/home/wink/prgs/python/projects/act-failing-python-cache/.github/workflows'"
     4	time="2020-11-19T14:22:52-08:00" level=debug msg="Reading workflow '/home/wink/prgs/python/projects/act-failing-python-cache/.github/workflows/python_cache.yml'"
     5	time="2020-11-19T14:22:52-08:00" level=debug msg="Planning event: push"
     6	time="2020-11-19T14:22:52-08:00" level=debug msg="Loading slug from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
     7	time="2020-11-19T14:22:52-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
     8	time="2020-11-19T14:22:52-08:00" level=debug msg="Loading revision from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
     9	time="2020-11-19T14:22:52-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
    10	time="2020-11-19T14:22:52-08:00" level=debug msg="HEAD points to 'ca084655394632c5afb4c7a5f78108b30d1d133e'"
    11	time="2020-11-19T14:22:52-08:00" level=debug msg="HEAD matches refs/heads/main"
    12	time="2020-11-19T14:22:52-08:00" level=debug msg="using github ref: refs/heads/main"
    13	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: RunContext vmEnv rc.GetEnv=map[ACT:true]"
    14	time="2020-11-19T14:22:52-08:00" level=debug msg="WIHK: RunContext.func:+ vmEnv call ################# vm.Set(\"env\", rc.GetEnv()=map[ACT:true])"
    15	time="2020-11-19T14:22:52-08:00" level=debug msg="WIHK: RunContext.func:- vmEnv"
    16	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: NewPlanExecutor jobName='python_cache/test' ctx=&context.valueCtx{Context:(*context.cancelCtx)(0xc0003e81c0), key:\"dryrun\", val:false}"
    17	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: NewPlanExecutor ctx v=&context.valueCtx{Context:(*context.cancelCtx)(0xc0003e81c0), key:\"dryrun\", val:false}"
    18	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: NewPlanExecutor ctx +v=&context.valueCtx{Context:(*context.cancelCtx)(0xc0003e81c0), key:\"dryrun\", val:false}"
    19	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: NewPlanExecutor ctx #v=&context.valueCtx{Context:(*context.cancelCtx)(0xc0003e81c0), key:\"dryrun\", val:false}"
    20	[python_cache/test] 🚀  Start image=winksaville/ubuntu-dev:20.04
    21	time="2020-11-19T14:22:52-08:00" level=debug msg="Loading slug from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
    22	time="2020-11-19T14:22:52-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
    23	time="2020-11-19T14:22:52-08:00" level=debug msg="Loading revision from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
    24	time="2020-11-19T14:22:52-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
    25	time="2020-11-19T14:22:52-08:00" level=debug msg="HEAD points to 'ca084655394632c5afb4c7a5f78108b30d1d133e'"
    26	time="2020-11-19T14:22:52-08:00" level=debug msg="HEAD matches refs/heads/main"
    27	time="2020-11-19T14:22:52-08:00" level=debug msg="using github ref: refs/heads/main"
    28	[python_cache/test]   🐳  docker pull winksaville/ubuntu-dev:20.04
    29	time="2020-11-19T14:22:52-08:00" level=debug msg="Image exists? true"
    30	[python_cache/test] Removed container: 50e43a4d2c2544cdda0779858fb86709e207ffc6dc5f2bc9b595087625c7be43
    31	[python_cache/test]   🐳  docker volume rm act-python-cache-test
    32	[python_cache/test]   🐳  docker create image=winksaville/ubuntu-dev:20.04 entrypoint=["/usr/bin/tail" "-f" "/dev/null"] cmd=[]
    33	[python_cache/test] Created container name=act-python-cache-test id=9b140e1698a9143161fa516ee1e570ffbbf4ce984613291a40bc362e43d20f93 from image winksaville/ubuntu-dev:20.04
    34	[python_cache/test] ENV ==> [RUNNER_TOOL_CACHE=/opt/hostedtoolcache RUNNER_OS=Linux RUNNER_TEMP=/tmp]
    35	[python_cache/test]   🐳  docker run image=winksaville/ubuntu-dev:20.04 entrypoint=["/usr/bin/tail" "-f" "/dev/null"] cmd=[]
    36	[python_cache/test] Starting container: 9b140e1698a9143161fa516ee1e570ffbbf4ce984613291a40bc362e43d20f93
    37	[python_cache/test] Started container: 9b140e1698a9143161fa516ee1e570ffbbf4ce984613291a40bc362e43d20f93
    38	time="2020-11-19T14:22:52-08:00" level=debug msg="Writing entry to tarball workflow/event.json len:2"
    39	time="2020-11-19T14:22:52-08:00" level=debug msg="Writing entry to tarball home/.act len:0"
    40	[python_cache/test] Extracting content to '/github/'
    41	time="2020-11-19T14:22:52-08:00" level=debug msg="WiNK: call sc.setupEnv()(ctx)"
    42	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv rc.GetEnv()=map[ACT:true]"
    43	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv step.GetEnv()=map[INPUT_PYTHON-VERSION:3.7]"
    44	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv.func:+ *********************"
    45	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv.func: rc.GetEnv()=map[ACT:true]"
    46	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv.func: step.GetEnv()=map[INPUT_PYTHON-VERSION:3.7]"
    47	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv.func mergeMaps 2 rc, step"
    48	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv.func env=map[ACT:true INPUT_PYTHON-VERSION:3.7]"
    49	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv.func Interpolate k=ACT v=true env[k]=true"
    50	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv.func Interpolate env[k]=true"
    51	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv.func Interpolate k=INPUT_PYTHON-VERSION v=3.7 env[k]=3.7"
    52	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv.func Interpolate env[k]=3.7"
    53	time="2020-11-19T14:22:52-08:00" level=debug msg="Loading slug from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
    54	time="2020-11-19T14:22:52-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
    55	time="2020-11-19T14:22:52-08:00" level=debug msg="Loading revision from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
    56	time="2020-11-19T14:22:52-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
    57	time="2020-11-19T14:22:52-08:00" level=debug msg="HEAD points to 'ca084655394632c5afb4c7a5f78108b30d1d133e'"
    58	time="2020-11-19T14:22:52-08:00" level=debug msg="HEAD matches refs/heads/main"
    59	time="2020-11-19T14:22:52-08:00" level=debug msg="using github ref: refs/heads/main"
    60	time="2020-11-19T14:22:52-08:00" level=debug msg="setupEnv => map[ACT:true CI:true GITHUB_ACTION:0 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_PYTHON-VERSION:3.7]"
    61	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupEnv.func:- ******************************"
    62	time="2020-11-19T14:22:52-08:00" level=debug msg="Loading slug from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
    63	time="2020-11-19T14:22:52-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
    64	time="2020-11-19T14:22:52-08:00" level=debug msg="Loading revision from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
    65	time="2020-11-19T14:22:52-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
    66	time="2020-11-19T14:22:52-08:00" level=debug msg="HEAD points to 'ca084655394632c5afb4c7a5f78108b30d1d133e'"
    67	time="2020-11-19T14:22:52-08:00" level=debug msg="HEAD matches refs/heads/main"
    68	time="2020-11-19T14:22:52-08:00" level=debug msg="using github ref: refs/heads/main"
    69	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: RunContext vmEnv rc.GetEnv=map[ACT:true]"
    70	time="2020-11-19T14:22:52-08:00" level=debug msg="WIHK: RunContext.func:+ vmEnv call ################# vm.Set(\"env\", rc.GetEnv()=map[ACT:true])"
    71	time="2020-11-19T14:22:52-08:00" level=debug msg="WIHK: RunContext.func:- vmEnv"
    72	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: StepContext vmEnv sc.GetEnv=map[ACT:true CI:true GITHUB_ACTION:0 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_PYTHON-VERSION:3.7]"
    73	time="2020-11-19T14:22:52-08:00" level=debug msg="WIHK: StepContext.func:+ vmEnv call !!!!!!!!!!!!!!!!! vm.Set(\"env\", sc.Env=map[ACT:true CI:true GITHUB_ACTION:0 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_PYTHON-VERSION:3.7])"
    74	time="2020-11-19T14:22:52-08:00" level=debug msg="WIHK: StepContext.func:- vmEnv"
    75	time="2020-11-19T14:22:52-08:00" level=debug msg="WiNK: done NewExpressionEvaluator()"
    76	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: newStepExecutor.func:+ Run &{RunContext:python_cache/test Step:step1 Env:map[ACT:true CI:true GITHUB_ACTION:0 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_PYTHON-VERSION:3.7] Cmd:[] Action:<nil>}"
    77	[python_cache/test] ⭐  Run step1
    78	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK:+ Executor: sc=&{RunContext:python_cache/test Step:step1 Env:map[ACT:true CI:true GITHUB_ACTION:0 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_PYTHON-VERSION:3.7] Cmd:[] Action:<nil>}"
    79	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: setupAction: sc=&{RunContext:python_cache/test Step:step1 Env:map[ACT:true CI:true GITHUB_ACTION:0 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_PYTHON-VERSION:3.7] Cmd:[] Action:<nil>} actionDir=/home/wink/.cache/act/actions-setup-python@v2 actionPath="
    80	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK: runAction: sc=&{RunContext:python_cache/test Step:step1 Env:map[ACT:true CI:true GITHUB_ACTION:0 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_PYTHON-VERSION:3.7] Cmd:[] Action:<nil>}"
    81	time="2020-11-19T14:22:52-08:00" level=debug msg="WINK:- Executor: sc=&{RunContext:python_cache/test Step:step1 Env:map[ACT:true CI:true GITHUB_ACTION:0 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_PYTHON-VERSION:3.7] Cmd:[] Action:<nil>}"
    82	[python_cache/test]   ☁  git clone 'https://github.com/actions/setup-python' # ref=v2
    83	[python_cache/test]   cloning https://github.com/actions/setup-python to /home/wink/.cache/act/actions-setup-python@v2
    84	[python_cache/test] Cloned https://github.com/actions/setup-python to /home/wink/.cache/act/actions-setup-python@v2
    85	[python_cache/test] Checked out v2
    86	time="2020-11-19T14:22:53-08:00" level=debug msg="Read action &{Setup Python GitHub Set up a specific version of Python and add the command-line tools to the PATH. map[architecture:{The target architecture (x86, x64) of the Python interpreter. false } python-version:{Version range or exact version of a Python version to use, using SemVer's version range syntax. false 3.x} token:{Used to pull python distributions from actions/python-versions. Since there's a default, this is typically not supplied by the user. false ${{ github.token }}}] map[python-version:{The installed python version. Useful when given a version range as input.}] {node12 map[] dist/index.js  [] []} {yellow code}} from '/home/wink/.cache/act/actions-setup-python@v2/action.yml'"
    87	time="2020-11-19T14:22:53-08:00" level=debug msg="About to run action &{Setup Python GitHub Set up a specific version of Python and add the command-line tools to the PATH. map[architecture:{The target architecture (x86, x64) of the Python interpreter. false } python-version:{Version range or exact version of a Python version to use, using SemVer's version range syntax. false 3.x} token:{Used to pull python distributions from actions/python-versions. Since there's a default, this is typically not supplied by the user. false ${{ github.token }}}] map[python-version:{The installed python version. Useful when given a version range as input.}] {node12 map[] dist/index.js  [] []} {yellow code}}"
    88	time="2020-11-19T14:22:53-08:00" level=debug msg="Evaluating 'github['token']' instead of 'github.token'"
    89	time="2020-11-19T14:22:53-08:00" level=debug msg="WINK: Evaluated ret: 'github['token']' val=''"
    90	time="2020-11-19T14:22:53-08:00" level=debug msg="type=3 actionDir=/home/wink/.cache/act/actions-setup-python@v2 actionPath= Workdir=/home/wink/prgs/python/projects/act-failing-python-cache ActionCacheDir=/home/wink/.cache/act actionName=actions-setup-python@v2 containerActionDir=/actions"
    91	time="2020-11-19T14:22:53-08:00" level=debug msg="Removing /home/wink/.cache/act/actions-setup-python@v2/.gitignore before docker cp"
    92	[python_cache/test]   🐳  docker cp src=/home/wink/.cache/act/actions-setup-python@v2 dst=/actions/
    93	[python_cache/test] Exec command '[mkdir -p /actions/]'
    94	time="2020-11-19T14:22:53-08:00" level=debug msg="Writing tarball /tmp/act186201686 from /home/wink/.cache/act/actions-setup-python@v2"
    95	time="2020-11-19T14:22:53-08:00" level=debug msg="Stripping prefix:/home/wink/.cache/act/ src:/home/wink/.cache/act/actions-setup-python@v2"
    96	[python_cache/test] Extracting content from '/tmp/act186201686' to '/actions/'
    97	time="2020-11-19T14:22:53-08:00" level=debug msg="executing remote job container: [node /actions/actions-setup-python@v2/dist/index.js]"
    98	[python_cache/test] Exec command '[node /actions/actions-setup-python@v2/dist/index.js]'
    99	[python_cache/test]   💬  ::debug::Semantic version spec of 3.7 is 3.7
   100	[python_cache/test]   💬  ::debug::isExplicit: 
   101	[python_cache/test]   💬  ::debug::explicit? false
   102	[python_cache/test]   💬  ::debug::evaluating 0 versions
   103	[python_cache/test]   💬  ::debug::match not found
   104	[python_cache/test]   | Version 3.7 was not found in the local cache
   105	[python_cache/test]   💬  ::debug::check 3.10.0-alpha.2 satisfies 3.7
   106	[python_cache/test]   💬  ::debug::check 3.10.0-alpha.1 satisfies 3.7
   107	[python_cache/test]   💬  ::debug::check 3.9.0 satisfies 3.7
   108	[python_cache/test]   💬  ::debug::check 3.9.0-rc.2 satisfies 3.7
   109	[python_cache/test]   💬  ::debug::check 3.9.0-rc.1 satisfies 3.7
   110	[python_cache/test]   💬  ::debug::check 3.9.0-beta.5 satisfies 3.7
   111	[python_cache/test]   💬  ::debug::check 3.9.0-beta.4 satisfies 3.7
   112	[python_cache/test]   💬  ::debug::check 3.8.6 satisfies 3.7
   113	[python_cache/test]   💬  ::debug::check 3.8.5 satisfies 3.7
   114	[python_cache/test]   💬  ::debug::check 3.8.4 satisfies 3.7
   115	[python_cache/test]   💬  ::debug::check 3.8.3 satisfies 3.7
   116	[python_cache/test]   💬  ::debug::check 3.8.2 satisfies 3.7
   117	[python_cache/test]   💬  ::debug::check 3.8.1 satisfies 3.7
   118	[python_cache/test]   💬  ::debug::check 3.8.0 satisfies 3.7
   119	[python_cache/test]   💬  ::debug::check 3.7.9 satisfies 3.7
   120	[python_cache/test]   💬  ::debug::x64===x64 && darwin===linux
   121	[python_cache/test]   💬  ::debug::x64===x64 && linux===linux
   122	[python_cache/test]   💬  ::debug::x64===x64 && linux===linux
   123	[python_cache/test]   💬  ::debug::x64===x64 && linux===linux
   124	[python_cache/test]   💬  ::debug::matched 3.7.9
   125	[python_cache/test]   | Version 3.7 is available for downloading
   126	[python_cache/test]   | Download from "https://github.com/actions/python-versions/releases/download/3.7.9-86953/python-3.7.9-linux-20.04-x64.tar.gz"
   127	[python_cache/test]   💬  ::debug::Downloading https://github.com/actions/python-versions/releases/download/3.7.9-86953/python-3.7.9-linux-20.04-x64.tar.gz
   128	[python_cache/test]   💬  ::debug::Destination /tmp/3639dfc2-3cd4-4a91-877d-9c138411b3ae
   129	[python_cache/test]   💬  ::debug::download complete
   130	[python_cache/test]   | Extract downloaded archive
   131	[python_cache/test]   💬  ::debug::Checking tar --version
   132	[python_cache/test]   💬  ::debug::tar (GNU tar) 1.30%0ACopyright (C) 2017 Free Software Foundation, Inc.%0ALicense GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>.%0AThis is free software: you are free to change and redistribute it.%0AThere is NO WARRANTY, to the extent permitted by law.%0A%0AWritten by John Gilmore and Jay Fenlason.
   133	[python_cache/test]   | [command]/usr/bin/tar xz --warning=no-unknown-keyword -C /tmp/5dbb1a9c-3a0d-4ac6-9c2d-651a818d3248 -f /tmp/3639dfc2-3cd4-4a91-877d-9c138411b3ae
   134	[python_cache/test]   | Execute installation script
   135	[python_cache/test]   | Check if Python hostedtoolcache folder exist...
   136	[python_cache/test]   | Creating Python hostedtoolcache folder...
   137	[python_cache/test]   | Create Python 3.7.9 folder
   138	[python_cache/test]   | Copy Python binaries to hostedtoolcache folder
   139	[python_cache/test]   | Create additional symlinks (Required for the UsePythonVersion Azure Pipelines task and the setup-python GitHub Action)
   140	[python_cache/test]   | Upgrading PIP...
   141	[python_cache/test]   | Looking in links: /tmp/tmpenkfk0ge
   142	[python_cache/test]   | Requirement already satisfied: setuptools in /opt/hostedtoolcache/Python/3.7.9/x64/lib/python3.7/site-packages (47.1.0)
   143	[python_cache/test]   | Requirement already satisfied: pip in /opt/hostedtoolcache/Python/3.7.9/x64/lib/python3.7/site-packages (20.1.1)
   144	[python_cache/test]   | Collecting pip
   145	[python_cache/test]   | Downloading pip-20.2.4-py2.py3-none-any.whl (1.5 MB)
   146	[python_cache/test]   | Installing collected packages: pip
   147	[python_cache/test]   | Successfully installed pip-20.2.4
   148	[python_cache/test]   | Create complete file
   149	[python_cache/test]   💬  ::debug::isExplicit: 
   150	[python_cache/test]   💬  ::debug::explicit? false
   151	[python_cache/test]   💬  ::debug::isExplicit: 3.7.9
   152	[python_cache/test]   💬  ::debug::explicit? true
   153	[python_cache/test]   💬  ::debug::evaluating 1 versions
   154	[python_cache/test]   💬  ::debug::matched: 3.7.9
   155	[python_cache/test]   💬  ::debug::checking cache: /opt/hostedtoolcache/Python/3.7.9/x64
   156	[python_cache/test]   💬  ::debug::Found tool in cache Python 3.7.9 x64
   157	[python_cache/test]   ⚙  ::set-env:: pythonLocation=/opt/hostedtoolcache/Python/3.7.9/x64
   158	[python_cache/test]   ⚙  ::set-env:: LD_LIBRARY_PATH=/opt/hostedtoolcache/Python/3.7.9/x64/lib
   159	[python_cache/test]   ⚙  ::add-path:: /opt/hostedtoolcache/Python/3.7.9/x64
   160	[python_cache/test]   ⚙  ::add-path:: /opt/hostedtoolcache/Python/3.7.9/x64/bin
   161	[python_cache/test]   ⚙  ::set-output:: python-version=3.7.9
   162	[python_cache/test]   | Successfully setup CPython (3.7.9)
   163	[python_cache/test]   ❓  ##[add-matcher]/actions/actions-setup-python@v2/.github/python.json
   164	[python_cache/test]   ✅  Success - step1
   165	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: newStepExecutor.func:- Run sc=&{RunContext:python_cache/test Step:step1 Env:map[ACT:true CI:true GITHUB_ACTION:0 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_ARCHITECTURE: INPUT_PYTHON-VERSION:3.7 INPUT_TOKEN:] Cmd:[] Action:0xc000459e10} rc.StepResults=&{Success:true Outputs:map[python-version:3.7.9]} rc.GetEnv=map[ACT:true LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64]"
   166	time="2020-11-19T14:23:03-08:00" level=debug msg="WiNK: call sc.setupEnv()(ctx)"
   167	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv rc.GetEnv()=map[ACT:true LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64]"
   168	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv step.GetEnv()=map[INPUT_KEY:${{ runner.os }}-python-${{ env.pythonLocation }} INPUT_PATH:${{ env.pythonLocation }}]"
   169	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func:+ *********************"
   170	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func: rc.GetEnv()=map[ACT:true LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64]"
   171	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func: step.GetEnv()=map[INPUT_KEY:${{ runner.os }}-python-${{ env.pythonLocation }} INPUT_PATH:${{ env.pythonLocation }}]"
   172	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func mergeMaps 2 rc, step"
   173	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func prepend ExtraPath=[/opt/hostedtoolcache/Python/3.7.9/x64 /opt/hostedtoolcache/Python/3.7.9/x64/bin]"
   174	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func env=map[ACT:true INPUT_KEY:${{ runner.os }}-python-${{ env.pythonLocation }} INPUT_PATH:${{ env.pythonLocation }} LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib PATH:/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64]"
   175	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate k=pythonLocation v=/opt/hostedtoolcache/Python/3.7.9/x64 env[k]=/opt/hostedtoolcache/Python/3.7.9/x64"
   176	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate env[k]=/opt/hostedtoolcache/Python/3.7.9/x64"
   177	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate k=LD_LIBRARY_PATH v=/opt/hostedtoolcache/Python/3.7.9/x64/lib env[k]=/opt/hostedtoolcache/Python/3.7.9/x64/lib"
   178	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate env[k]=/opt/hostedtoolcache/Python/3.7.9/x64/lib"
   179	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate k=ACT v=true env[k]=true"
   180	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate env[k]=true"
   181	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate k=INPUT_KEY v=${{ runner.os }}-python-${{ env.pythonLocation }} env[k]=${{ runner.os }}-python-${{ env.pythonLocation }}"
   182	time="2020-11-19T14:23:03-08:00" level=debug msg="Evaluating 'runner['os']' instead of 'runner.os'"
   183	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: Evaluated ret: 'runner['os']' val='Linux'"
   184	time="2020-11-19T14:23:03-08:00" level=debug msg="Evaluating 'env['pythonLocation']' instead of 'env.pythonLocation'"
   185	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: Evaluated val=undefined ret: val=\"\", false, err='<nil>'"
   186	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate env[k]=Linux-python-"
   187	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate k=INPUT_PATH v=${{ env.pythonLocation }} env[k]=${{ env.pythonLocation }}"
   188	time="2020-11-19T14:23:03-08:00" level=debug msg="Evaluating 'env['pythonLocation']' instead of 'env.pythonLocation'"
   189	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: Evaluated val=undefined ret: val=\"\", false, err='<nil>'"
   190	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate env[k]="
   191	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate k=PATH v=/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl env[k]=/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl"
   192	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func Interpolate env[k]=/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl"
   193	time="2020-11-19T14:23:03-08:00" level=debug msg="Loading slug from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
   194	time="2020-11-19T14:23:03-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
   195	time="2020-11-19T14:23:03-08:00" level=debug msg="Loading revision from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
   196	time="2020-11-19T14:23:03-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
   197	time="2020-11-19T14:23:03-08:00" level=debug msg="HEAD points to 'ca084655394632c5afb4c7a5f78108b30d1d133e'"
   198	time="2020-11-19T14:23:03-08:00" level=debug msg="HEAD matches refs/heads/main"
   199	time="2020-11-19T14:23:03-08:00" level=debug msg="using github ref: refs/heads/main"
   200	time="2020-11-19T14:23:03-08:00" level=debug msg="setupEnv => map[ACT:true CI:true GITHUB_ACTION:1 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_KEY:Linux-python- INPUT_PATH: LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib PATH:/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64]"
   201	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupEnv.func:- ******************************"
   202	time="2020-11-19T14:23:03-08:00" level=debug msg="Loading slug from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
   203	time="2020-11-19T14:23:03-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
   204	time="2020-11-19T14:23:03-08:00" level=debug msg="Loading revision from git directory '/home/wink/prgs/python/projects/act-failing-python-cache/.git'"
   205	time="2020-11-19T14:23:03-08:00" level=debug msg="Found revision: ca084655394632c5afb4c7a5f78108b30d1d133e\n"
   206	time="2020-11-19T14:23:03-08:00" level=debug msg="HEAD points to 'ca084655394632c5afb4c7a5f78108b30d1d133e'"
   207	time="2020-11-19T14:23:03-08:00" level=debug msg="HEAD matches refs/heads/main"
   208	time="2020-11-19T14:23:03-08:00" level=debug msg="using github ref: refs/heads/main"
   209	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: RunContext vmEnv rc.GetEnv=map[ACT:true LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64]"
   210	time="2020-11-19T14:23:03-08:00" level=debug msg="WIHK: RunContext.func:+ vmEnv call ################# vm.Set(\"env\", rc.GetEnv()=map[ACT:true LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64])"
   211	time="2020-11-19T14:23:03-08:00" level=debug msg="WIHK: RunContext.func:- vmEnv"
   212	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: StepContext vmEnv sc.GetEnv=map[ACT:true CI:true GITHUB_ACTION:1 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_KEY:Linux-python- INPUT_PATH: LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib PATH:/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64]"
   213	time="2020-11-19T14:23:03-08:00" level=debug msg="WIHK: StepContext.func:+ vmEnv call !!!!!!!!!!!!!!!!! vm.Set(\"env\", sc.Env=map[ACT:true CI:true GITHUB_ACTION:1 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_KEY:Linux-python- INPUT_PATH: LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib PATH:/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64])"
   214	time="2020-11-19T14:23:03-08:00" level=debug msg="WIHK: StepContext.func:- vmEnv"
   215	time="2020-11-19T14:23:03-08:00" level=debug msg="WiNK: done NewExpressionEvaluator()"
   216	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: newStepExecutor.func:+ Run &{RunContext:python_cache/test Step:step2 Env:map[ACT:true CI:true GITHUB_ACTION:1 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_KEY:Linux-python- INPUT_PATH: LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib PATH:/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64] Cmd:[] Action:<nil>}"
   217	[python_cache/test] ⭐  Run step2
   218	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK:+ Executor: sc=&{RunContext:python_cache/test Step:step2 Env:map[ACT:true CI:true GITHUB_ACTION:1 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_KEY:Linux-python- INPUT_PATH: LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib PATH:/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64] Cmd:[] Action:<nil>}"
   219	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: setupAction: sc=&{RunContext:python_cache/test Step:step2 Env:map[ACT:true CI:true GITHUB_ACTION:1 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_KEY:Linux-python- INPUT_PATH: LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib PATH:/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64] Cmd:[] Action:<nil>} actionDir=/home/wink/.cache/act/actions-cache@v2 actionPath="
   220	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK: runAction: sc=&{RunContext:python_cache/test Step:step2 Env:map[ACT:true CI:true GITHUB_ACTION:1 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_KEY:Linux-python- INPUT_PATH: LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib PATH:/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64] Cmd:[] Action:<nil>}"
   221	time="2020-11-19T14:23:03-08:00" level=debug msg="WINK:- Executor: sc=&{RunContext:python_cache/test Step:step2 Env:map[ACT:true CI:true GITHUB_ACTION:1 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_KEY:Linux-python- INPUT_PATH: LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib PATH:/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64] Cmd:[] Action:<nil>}"
   222	[python_cache/test]   ☁  git clone 'https://github.com/actions/cache' # ref=v2
   223	[python_cache/test]   cloning https://github.com/actions/cache to /home/wink/.cache/act/actions-cache@v2
   224	[python_cache/test] Cloned https://github.com/actions/cache to /home/wink/.cache/act/actions-cache@v2
   225	[python_cache/test] Checked out v2
   226	time="2020-11-19T14:23:04-08:00" level=debug msg="Read action &{Cache GitHub Cache artifacts like dependencies and build outputs to improve workflow execution time map[key:{An explicit key for restoring and saving the cache true } path:{A list of files, directories, and wildcard patterns to cache and restore true } restore-keys:{An ordered list of keys to use for restoring the cache if no cache hit occurred for key false } upload-chunk-size:{The chunk size used to split up large files during upload, in bytes false }] map[cache-hit:{A boolean value to indicate an exact match was found for the primary key}] {node12 map[] dist/restore/index.js  [] []} {gray-dark archive}} from '/home/wink/.cache/act/actions-cache@v2/action.yml'"
   227	time="2020-11-19T14:23:04-08:00" level=debug msg="About to run action &{Cache GitHub Cache artifacts like dependencies and build outputs to improve workflow execution time map[key:{An explicit key for restoring and saving the cache true } path:{A list of files, directories, and wildcard patterns to cache and restore true } restore-keys:{An ordered list of keys to use for restoring the cache if no cache hit occurred for key false } upload-chunk-size:{The chunk size used to split up large files during upload, in bytes false }] map[cache-hit:{A boolean value to indicate an exact match was found for the primary key}] {node12 map[] dist/restore/index.js  [] []} {gray-dark archive}}"
   228	time="2020-11-19T14:23:04-08:00" level=debug msg="type=3 actionDir=/home/wink/.cache/act/actions-cache@v2 actionPath= Workdir=/home/wink/prgs/python/projects/act-failing-python-cache ActionCacheDir=/home/wink/.cache/act actionName=actions-cache@v2 containerActionDir=/actions"
   229	time="2020-11-19T14:23:04-08:00" level=debug msg="Removing /home/wink/.cache/act/actions-cache@v2/.gitignore before docker cp"
   230	[python_cache/test]   🐳  docker cp src=/home/wink/.cache/act/actions-cache@v2 dst=/actions/
   231	[python_cache/test] Exec command '[mkdir -p /actions/]'
   232	time="2020-11-19T14:23:04-08:00" level=debug msg="Writing tarball /tmp/act484812733 from /home/wink/.cache/act/actions-cache@v2"
   233	time="2020-11-19T14:23:04-08:00" level=debug msg="Stripping prefix:/home/wink/.cache/act/ src:/home/wink/.cache/act/actions-cache@v2"
   234	[python_cache/test] Extracting content from '/tmp/act484812733' to '/actions/'
   235	time="2020-11-19T14:23:04-08:00" level=debug msg="executing remote job container: [node /actions/actions-cache@v2/dist/restore/index.js]"
   236	[python_cache/test] Exec command '[node /actions/actions-cache@v2/dist/restore/index.js]'
   237	[python_cache/test]   ❓  ::save-state name=CACHE_KEY::Linux-python-
   238	[python_cache/test]   ❗  ::error::Input required and not supplied: path
   239	[python_cache/test]   | (node:62) [DEP0091] DeprecationWarning: crypto.DEFAULT_ENCODING is deprecated.
   240	[python_cache/test]   | (node:62) [DEP0010] DeprecationWarning: crypto.createCredentials is deprecated. Use tls.createSecureContext instead.
   241	[python_cache/test]   | (node:62) [DEP0011] DeprecationWarning: crypto.Credentials is deprecated. Use tls.SecureContext instead.
   242	[python_cache/test]   ❌  Failure - step2
   243	time="2020-11-19T14:23:05-08:00" level=debug msg="WINK: newStepExecutor.func:- Run sc=&{RunContext:python_cache/test Step:step2 Env:map[ACT:true CI:true GITHUB_ACTION:1 GITHUB_ACTIONS:true GITHUB_ACTOR:nektos/act GITHUB_EVENT_NAME:push GITHUB_EVENT_PATH:/github/workflow/event.json GITHUB_REF:refs/heads/main GITHUB_REPOSITORY:winksaville/act-failing-python-cache GITHUB_RUN_ID:1 GITHUB_RUN_NUMBER:1 GITHUB_SHA:ca084655394632c5afb4c7a5f78108b30d1d133e GITHUB_TOKEN: GITHUB_WORKFLOW:python_cache GITHUB_WORKSPACE:/github/workspace HOME:/github/home INPUT_KEY:Linux-python- INPUT_PATH: INPUT_RESTORE-KEYS: INPUT_UPLOAD-CHUNK-SIZE: LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib PATH:/opt/hostedtoolcache/Python/3.7.9/x64:/opt/hostedtoolcache/Python/3.7.9/x64/bin:./node_modules/.bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.pub-cache/bin:/home/wink/prgs/flutter/flutter/bin:/home/wink/.cargo/bin:/home/wink/go/bin:/home/wink/.npm-globalX/bin:/home/wink/Android/Sdk/emulator:/home/wink/Android/Sdk/platform-tools:/home/wink/Android/Sdk/tools/bin:/home/wink/Android/Sdk/tools:/home/wink/bin:/home/wink/.cargo/bin:/opt/miniconda3/condabin:/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/opt/cuda/bin:/opt/cuda/integration/nsight-compute:/opt/cuda/integration/nsight-systems:/usr/lib/jvm/default/bin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64] Cmd:[] Action:0xc0004196c0} rc.StepResults=&{Success:false Outputs:map[]} rc.GetEnv=map[ACT:true LD_LIBRARY_PATH:/opt/hostedtoolcache/Python/3.7.9/x64/lib pythonLocation:/opt/hostedtoolcache/Python/3.7.9/x64]"
   244	time="2020-11-19T14:23:05-08:00" level=debug msg="exit with `FAILURE`: 1"
   245	time="2020-11-19T14:23:05-08:00" level=debug msg="exit with `FAILURE`: 1"
   246	Error: exit with `FAILURE`: 1
```
