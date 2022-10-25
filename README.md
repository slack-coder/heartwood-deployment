# Project Name

Use Git to deploy your service via a recipe of common command line tools.  This has been
adjusted for Radicle's heartwood.

## Benefits

 - Modifiable
 - Portable
 - Simple

## Setup

Install the dependencies listed in 'etc/dependency'.

Create the service's source code repository:
```
git clone https://github.com/radicle-dev/heartwood src/heartwood
cp post-update src/heartwood/.git/hooks

# Allow updating the filesystem on git push
cd src/heartwood
git config --add receive.denyCurrentBranch ignore
```

Then configure the environment variables to suit your system using your
favorite editor.

```
edit etc/environment
```

You may want do a build of the service now.  It is required for the next step,
and rustup specific changes may be needed.

```
(
  . etc/environment
  cd src/heartwood
  build
)
```

## Use

The service must be started first.  It will start under a tmux session.
Dettach using cntr-b+d to leave it running in the background.

To support heartwood, an ssh-agent session must be started and the user must be
authorized using `rad-auth`.

```
eval `ssh-agent`
rad-auth
./bin/start
```

To inspect the service, attach back to it using tmux:

```
tmux attach -t heartwood
```

Deploy the latest version by pushing to it using git:

```
git remote add deploy [path to service]
git push -u deploy master
```

To stop the service, kill the tmux session via stop:

```
./bin/stop
```

## Directory Layout

 - /bin: where all commands go
 - /bin/start: shell script to start the service
 - /bin/build: shell script to build the service
 - /bin/stop: shell script to stop the service
 - /etc: system configuration
 - /etc/dependency: list of dependencies to deploy and build service
 - /etc/environment: globally defined environment variables
 - /src: any project source code, there could be more than one
 - /var: persistent storage for build and services

## Known Problems

### Service should retry on fail

The system is configured to only restart the service on a new push.  The are no
notifications or efforts made to restart the service.

### Tmux session may conflict with existing ones

The tmux session uses the name 'heartwood'.

### Rustup could be described better

Steps more specific to your intended setup are needed before building the
service.
