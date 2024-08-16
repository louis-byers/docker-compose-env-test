# docker-compose-env-test

Tests getting env values to commands in docker compose.

Run everything from this directory.

I found I had some issue trying to understand how it all works.

As I understand it, examples 1 & 3 try to use docker compose to substitute values. These ended up working when provided the env file in a certain way.

Examples 2 & 4 are just trying to get a shell variable in the command so it should pull it from the running container env, but this never actually worked. Weird because that is what I thought the `$$` was for.

## Example 1

Just trying to get the env with the `env_file` directive and single `$` in the command.

- ✅ Displaying env has value, i.e it should work
- ❌ Echo command shows value, i.e. it works

```shell
docker compose -f local-dev-docker-support/docker-compose-ex1.yml up
```

Output:

```shell
WARN[0000] The "TEST_VALUE" variable is not set. Defaulting to a blank string. 
[+] Running 1/0
 ✔ Container local-dev-docker-support-echo-test-1  Recreated                                                                                                                                                                          0.1s 
Attaching to echo-test-1
echo-test-1  | TEST_VALUE=someTestValue
echo-test-1  | HOSTNAME=ecc6b406d7b8
echo-test-1  | PWD=/
echo-test-1  | HOME=/root
echo-test-1  | SHLVL=1
echo-test-1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
echo-test-1  | _=/usr/bin/env
echo-test-1  | Test Value from env TEST_VALUE: 
echo-test-1 exited with code 0
```

## Example 1a

Just trying to get the env with the `env_file` directive and single `$` in the command.

- ✅ Displaying env has value, i.e it should work
- ✅ Echo command shows value, i.e. it works

```shell
docker compose -f local-dev-docker-support/docker-compose-ex1.yml --env-file .env up
```

Output:

```shell
[+] Running 1/0
 ✔ Container local-dev-docker-support-echo-test-1  Recreated                                                                                       0.0s 
Attaching to echo-test-1
echo-test-1  | TEST_VALUE=someTestValue
echo-test-1  | HOSTNAME=7a46fb97a3bb
echo-test-1  | PWD=/
echo-test-1  | HOME=/root
echo-test-1  | SHLVL=1
echo-test-1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
echo-test-1  | _=/usr/bin/env
echo-test-1  | Test Value from env TEST_VALUE: someTestValue
echo-test-1 exited with code 0

```

## Example 2

Just trying to get the env with the `env_file` directive and `$$` in the command.

- ✅ Displaying env has value, i.e it should work
- ❌ Echo command shows value, i.e. it works

```shell
docker compose -f local-dev-docker-support/docker-compose-ex2.yml up
```

Output:

```shell
[+] Running 1/0
 ✔ Container local-dev-docker-support-echo-test-1  Recreated                                                                                                                                                                          0.0s 
Attaching to echo-test-1
echo-test-1  | TEST_VALUE=someTestValue
echo-test-1  | HOSTNAME=325f39b3bbe3
echo-test-1  | PWD=/
echo-test-1  | HOME=/root
echo-test-1  | SHLVL=1
echo-test-1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
echo-test-1  | _=/usr/bin/env
echo-test-1  | Test Value from env TEST_VALUE: $TEST_VALUE
echo-test-1 exited with code 0
```

Oddity

```shell
docker compose -f local-dev-docker-support/docker-compose-ex2.yml --env-file .env run --entrypoint "/bin/bash" echo-test
```

 If you run th command `/bin/bash -c "env && echo 'Foo: $TEST_VALUE'"` you'll get what you think you will.

## Example 2a

Just trying to get the env with the `env_file` directive and `$$` in the command.

- ✅ Displaying env has value, i.e it should work
- ❌ Echo command shows value, i.e. it works

```shell
docker compose -f local-dev-docker-support/docker-compose-ex2.yml --env-file .env up
```

Output:

```shell
[+] Running 1/0
 ✔ Container local-dev-docker-support-echo-test-1  Recreated                                                                                                                                                                          0.0s 
Attaching to echo-test-1
echo-test-1  | TEST_VALUE=someTestValue
echo-test-1  | HOSTNAME=99068e352ce1
echo-test-1  | PWD=/
echo-test-1  | HOME=/root
echo-test-1  | SHLVL=1
echo-test-1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
echo-test-1  | _=/usr/bin/env
echo-test-1  | Test Value from env TEST_VALUE: $TEST_VALUE
echo-test-1 exited with code 0
```

Oddity

```shell
docker compose -f local-dev-docker-support/docker-compose-ex2.yml --env-file .env run --entrypoint "/bin/bash" echo-test
```

 If you run th command `/bin/bash -c "env && echo 'Foo: $TEST_VALUE'"` you'll get what you think you will.

## Example 3

Just trying to get the env with the `env_file` directive and an environment in the compose and `$` in the command.

- ❌ Displaying env has value, i.e it should work
- ❌ Echo command shows value, i.e. it works

```shell
docker compose -f local-dev-docker-support/docker-compose-ex3.yml up
```

Output:

```shell
WARN[0000] The "TEST_VALUE" variable is not set. Defaulting to a blank string. 
WARN[0000] The "TEST_VALUE" variable is not set. Defaulting to a blank string. 
[+] Running 1/0
 ✔ Container local-dev-docker-support-echo-test-1  Recreated                                                                                                                                                                          0.0s 
Attaching to echo-test-1
echo-test-1  | HOSTNAME=51f394028883
echo-test-1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
echo-test-1  | PWD=/aws
echo-test-1  | SHLVL=1
echo-test-1  | HOME=/root
echo-test-1  | TEST_VALUE=
echo-test-1  | _=/usr/bin/env
echo-test-1  | Test Value from env TEST_VALUE: 
echo-test-1 exited with code 0
```

## Example 3a

Just trying to get the env with the `env_file` directive and an environment in the compose and `$` in the command.

- ✅ Displaying env has value, i.e it should work
- ✅ Echo command shows value, i.e. it works

Add the env file to the docker compose command.

```shell
docker compose -f local-dev-docker-support/docker-compose-ex3.yml --env-file .env up
```

Output:

```shell
[+] Running 1/0
 ✔ Container local-dev-docker-support-echo-test-1  Recreated                                                                                                                                                                          0.0s 
Attaching to echo-test-1
echo-test-1  | HOSTNAME=235cd4f862f2
echo-test-1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
echo-test-1  | PWD=/aws
echo-test-1  | SHLVL=1
echo-test-1  | HOME=/root
echo-test-1  | TEST_VALUE=someTestValue
echo-test-1  | _=/usr/bin/env
echo-test-1  | Test Value from env TEST_VALUE: someTestValue
echo-test-1 exited with code 0
```

## Example 4

Just trying to get the env with the `env_file` directive and an environment in the compose and `$$` in the command.

- ❌ Displaying env has value, i.e it should work
- ❌ Echo command shows value, i.e. it works

```shell
docker compose -f local-dev-docker-support/docker-compose-ex4.yml up
```

Output:

```shell
WARN[0000] The "TEST_VALUE" variable is not set. Defaulting to a blank string. 
[+] Running 1/0
 ✔ Container local-dev-docker-support-echo-test-1  Recreated                                                                                                                                                                          0.0s 
Attaching to echo-test-1
echo-test-1  | HOSTNAME=3b3966dd1eed
echo-test-1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
echo-test-1  | PWD=/aws
echo-test-1  | SHLVL=1
echo-test-1  | HOME=/root
echo-test-1  | TEST_VALUE=
echo-test-1  | _=/usr/bin/env
echo-test-1  | Test Value from env TEST_VALUE: $TEST_VALUE
echo-test-1 exited with code 0
```

## Example 4a

Just trying to get the env with the `env_file` directive and an environment in the compose and `$$` in the command.

- ✅ Displaying env has value, i.e it should work
- ❌ Echo command shows value, i.e. it works

Add the env file to the docker compose command.

```shell
docker compose -f local-dev-docker-support/docker-compose-ex4.yml --env-file .env up
```
Output:

```shell
[+] Running 1/0
 ✔ Container local-dev-docker-support-echo-test-1  Recreated                                                                                                                                                                          0.0s 
Attaching to echo-test-1
echo-test-1  | HOSTNAME=376079da5799
echo-test-1  | PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
echo-test-1  | PWD=/aws
echo-test-1  | SHLVL=1
echo-test-1  | HOME=/root
echo-test-1  | TEST_VALUE=someTestValue
echo-test-1  | _=/usr/bin/env
echo-test-1  | Test Value from env TEST_VALUE: $TEST_VALUE
echo-test-1 exited with code 0

```
