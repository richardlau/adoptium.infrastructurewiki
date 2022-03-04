# Minimal test machine configuration

While we generally run the full ansible playbooks from this repository on all machines in order to set them up to be able to run builds or tests, this is not always necessary. Our [static docker containers](https://github.com/adoptium/infrastructure/tree/master/ansible/playbooks/AdoptOpenJDK_Unix_Playbook/roles/DockerStatic/Dockerfiles) on x64 and aarch64 for example do not run the playbooks but put a minimal setup on the machines over a wide variety of OS containers in order to minimise space usage by avoid all the prerequisites required for a build system.

Broadly speaking the packages required for testing are:
- git/wget/curl/unzip/which/hostname/gcc/ tools
- ant + ant-contrib
- a JDK, either in the PATH or a location set via `JAVA_HOME` (Maybe not?)
- Enought to run an Xvfb server e.g. the apropriate Xvfb/libXrender/libXi/LibXtst/fontconfig
- Appropriate language packs and `fakeroot` for certain activities
- Ensuring that the output from the `hostname` command is a resolvable name (i.e. defined in `/etc/hosts`) - check with `ping $(hostname)`

On macos systems, ant+ant-contrib and the JDK configured as above is typically sufficient on top of a default installation if the `/etc/hosts` is correct.

If the machines are to be used in jenkins, and appropriate user should be created and connected to the jenkins server by whatever means is practical for the system in question. (`useradd -m jenkins` on Linux or `sysadminctl -addUser jenkins` on macos
