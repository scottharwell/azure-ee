This is a simple execution environment definition that I created to use to test the Microsoft Azure Ansible collection.

1. Clone this repo
2. Run `ansible-builder build -t <YOUR_TAG_HERE>`

## Building on an M1 Mac

1. Update the included files in this repository with the variables to include in the EE. 

```yaml
additional_build_steps:
    append: |
        ADD ssh /home/runner/.ssh/
        RUN chmod 700 /home/runner/.ssh
        RUN chmod 600 /home/runner/.ssh/*
```

2. Run `ansible-builder create` to create the `context` directory.  This will include a newly created `Containerfile`.
3. If using RHEL docker images to build the container, then change the args at the top of the file.  Otherwise, move to the next step.
    * Statements will look like: `ARG EE_BASE_IMAGE=registry.redhat.io/ansible-automation-platform-21/ee-supported-rhel8:latest`
4. Add `--platform=amd64` to each `FROM` statement since most quay images are only built for the `linux/amd64` platform.
    * Statements will look like: `FROM --platform=amd64 $EE_BASE_IMAGE as galaxy`
5. Add any custom operations to the end of the Dockerfile that are required, such as copying SSH public keys.
6. Create directories in the `context` folder that are used to copy files, such as the `ssh` example above.

```bash
mkdir context/ssh
cp $HOME/.ssh/reusable_ssh_key.pub context/ssh
```

7. Change into the `context` directory and build a multi-platform image so that this can be used locally on your Mac for testing on M1 processors or with Ansible Controller on amd64 processors.

```bash
export REGISTRY=quay.io/scottharwell
export VERSION=0.0.1
docker buildx build --platform linux/arm64,linux/amd64 -t $REGISTRY/server-setup-ee:$VERSION -t $REGISTRY/server-setup-ee:latest --push .
```
