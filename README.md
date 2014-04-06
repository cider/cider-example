# How to build your project using Paprika #

1. Include `.paprika.yml` in your repository. This is optional but recommended.
2. Run `paprika build`, either pointing to your `.paprika.yml`, or specifying
   all the required parameters on the command line. The common practice should
   be to put `.paprika.yml` into the top level directory of the repository and
   simply run `paprika build` there.
3. Enjoy the output being streamed back into your console. You can interrupt
   the build by simply terminating `paprika`.

So, to put it together, there are three levels how to configure the build:

1. Include `.paprika.yml` in your project.
2. Use environment variables that `paprika build` understands.
3. Use `paprika build` flags.

Clearly `.paprika.yml` is the most practical solution since there is no need
to type long commands afterwards. On the other hand, you can trigger a build
with `.paprika.yml` completely missing, for example without even cloning the
project repository, if you specify all the parameters explicitly. The whole
`.paprika.yml` as presented in this repository could be skipped if `paprika
build` is run as

```bash
$ export PAPRIKA_MASTER_TOKEN='secret'
$ paprika build \
        -master 'wss://paprika.example.com/connect:443' \
		-slave 'any' \
        -repository 'git+https://github.com/paprikaci/paprika-example#master' \
        -script 'scripts/loop' \
		-runner 'bash' \
		-env 'ITERATIONS=10' -env 'MESSAGE=LOOP'
```

or

```bash
$ export PAPRIKA_MASTER_URL='wss://paprika.example.com/connect:443'
$ export PAPRIKA_MASTER_TOKEN='secret'
$ export PAPRIKA_SLAVE_LABEL='any'
$ export PAPRIKA_REPOSITORY_URL='git+https://github.com/paprikaci/paprika-example'
$ export PAPRIKA_SCRIPT_PATH='scripts/loop'
$ export PAPRIKA_SCRIPT_RUNNER='bash'
$ export PAPRIKA_SCRIPT_ENV_ITERATIONS='10'
$ export PAPRIKA_SCRIPT_ENV_MESSAGE='LOOP'
$ paprika build
```

The configuration options can be combined, e.g. you can overwrite the script
path on the command line while still getting the rest of the configuration from
`.paprika.yml`. Environmental variables can be also used to overwrite the static
configuration, but the command line flags always win the priority battle.

## Demo Build Run ##

```
$ paprika build
---> Connecting to wss://paprika.example.com/connect:443
---> Sending the build request (using slave label "any")
---> Locking the project workspace
---> Waiting for a free executor
---> Pulling the sources
Cloning into '/tmp/paprika/github.com/paprikaci/paprika-example/src'...
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
---> Build finished
Duration: 12.141151841s
```
