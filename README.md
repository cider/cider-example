# How to build your project using Cider #

First of all, you need a single script that represents all the build steps.
Cider then takes a repository URL and the relative path of that script within
the repository and runs the script on one of the build slaves. The script also
has its associated runner, which would be `bash` for Bash scripts, for example.

So, how to build your project using `cider build`:

1. Include `cider.yml` in your repository. This is optional but recommended.
2. Run `cider build`, either pointing to your `cider.yml`, or specifying
   all the required parameters on the command line. The common practice should
   be to put `cider.yml` into the top level directory of the repository and
   simply run `cider build` there.
3. Enjoy the output being streamed back into your console. You can interrupt
   the build by simply terminating `cider`.

To put it together, there are three levels how to configure the build:

1. Include `cider.yml` in your project.
2. Use environment variables that `cider build` understands.
3. Use `cider build` flags.

Clearly `cider.yml` is the most practical solution since there is no need
to type long commands afterwards. On the other hand, you can trigger a build
with `cider.yml` completely missing, for example without even cloning the
project repository, if you specify all the parameters explicitly. The whole
`cider.yml` as presented in this repository could be skipped if `cider
build` is run as

```bash
$ cider build \
    -master 'wss://cider.example.com:443/connect' \
    -token 'secret' \ # not the best thing you can do, though
    -slave 'any' \
    -repository 'git+https://github.com/cider/cider-example#master' \
    -script 'scripts/loop' \
    -runner 'bash' \
    -env 'ITERATIONS=10' -env 'MESSAGE=LOOP'
```

or

```bash
$ export CIDER_MASTER_URL='wss://cider.example.com:443/connect'
$ export CIDER_MASTER_TOKEN='secret'
$ export CIDER_SLAVE_LABEL='any'
$ export CIDER_REPOSITORY_URL='git+https://github.com/cider/cider-example'
$ export CIDER_SCRIPT_PATH='scripts/loop'
$ export CIDER_SCRIPT_RUNNER='bash'
$ export CIDER_SCRIPT_ENV_ITERATIONS='10'
$ export CIDER_SCRIPT_ENV_MESSAGE='LOOP'
$ cider build
```

The configuration options can be combined, e.g. you can overwrite the script
path on the command line while still getting the rest of the configuration from
`cider.yml`. Environmental variables can be also used to overwrite the static
configuration, but the command line flags always win the priority battle.

## Demo Build Run ##

### Success ###

```
$ cider build
---> Connecting to wss://cider.example.com:443/connect
---> Sending the build request (using method "cider.any.bash")
---> Locking the project workspace
---> Waiting for a free executor

---> Pulling the sources
Cloning into '/tmp/cider/github.com/cider/cider-example/src'...

---> Running the script located at scripts/loop (using runner "bash")
LOOP
LOOP
LOOP
LOOP
LOOP
LOOP
LOOP
LOOP
LOOP
LOOP

---> Build succeeded
Pull  duration:  2.055263854s
Build duration: 10.107692225s
Total duration: 12.162956079s
```

### Failure ###

In case the build gets stuck downloading the sources or executing, it can be
interrupted by simply terminating `cider` using CTRL + C or so. In that case
`cider` terminates with a non-zero exit status in much the same way as if the
build script itself would have failed:

```
$ cider build
---> Connecting to wss://cider.example.com:443/connect
---> Sending the build request (using method "cider.any.bash")
---> Locking the project workspace
---> Waiting for a free executor

---> Pulling the sources
From https://github.com//cider/cider-example
 * branch            master     -> FETCH_HEAD
   55b2599..b2f956c  master     -> origin/master
Already on 'master'
Your branch is behind 'origin/master' by 1 commit, and can be fast-forwarded.
  (use "git pull" to update your local branch)
Updating 55b2599..b2f956c
Fast-forward
 README.md | 5 ++++-
 1 file changed, 4 insertions(+), 1 deletion(-)

---> Running the script located at scripts/loop (using runner "bash")
LOOP
LOOP
LOOP
^C---> Interrupting the build job, this can take a few seconds

---> Build failed
Pull  duration: 4.286029948s 
Build duration: 3.134319047s 
Total duration: 7.420348995s 

Error: signal: terminated
$ echo $?
1
```
