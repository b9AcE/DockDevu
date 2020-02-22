# Docker Devuan ceres-specific information<br />
Build using a command akin to this,<br />
where "20200222-01" is to be replaced by the build-date and the build-serial,<br />
incremented by one for each build of that day:<br />
```docker build -f Dockerfile -t b9ace/devuan-ceres:latest -t b9ace/devuan-ceres:20200222-01 .```
<br /><br />
Push using a command akin to this:<br />
```docker push b9ace/devuan-ceres```<br />
<br /><br />
Run using a command akin to this, where "HostName.Example.Local" is the hostname you want set,<bt />
or skip that parameter to have it dynamically generetaed per container:<br />
```docker run --interactive --tty --rm --hostname HostName.Example.Local --dns 8.8.8.8 --dns 1.1.1.1 --dns 8.8.4.4 b9ace/devuan-ceres:latest```
