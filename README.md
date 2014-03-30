# How to build your project using Paprika #

1. Include `.paprika.yml` in your repository. This is optional but recommended.
2. Run `paprika build`, either pointing to your `.paprika.yml`, or specifying
   all the required parameters on the command line. The common practice should
   be to put `.paprika.yml` into the top level directory of the repository and
   simply run `paprika build` there.
3. Enjoy the output being streamed back into your console. You can interrupt
   the build by simply terminating `paprika`.

So, to put it together, there are three levels how to configure the build:

1. Use `paprika build` flags.
2. Use environment variables that `paprika build` understands.
3. Include `.paprika.yml` in your project.

Clearly `.paprika.yml` is the most practical solution since there is no need
to type long commands afterwards. On the other hand, you can trigger a build
with `.paprika.yml` completely missing, for example without even cloning the
project repository, if you specify all the parameters explicitly.

The configuration options can be combined, e.g. you can overwrite the script
path on the command line while still getting the rest of the configuration from
`.paprika.yml`. Environmental variables can be also used to overwrite the static
configuration, but the command line flags always win the priority battle.
