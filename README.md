This is a simple execution environment definition that I created to use to test the Microsoft Azure Ansible collection along with other collections and roles used for related automation.

## Building on an AMD64 Platform Linux or Mac Machine

Ansible Builder will function out-of-the-box on `amd64` platforms with Podman or Docker.

1. Clone this repo.
2. Update the included files in this repository with the variables to include in the EE.
   * `requirements.yml` for Ansible collections and roles.
   * `requirements.txt` for Python dependencies.
3. Run `ansible-builder build -t <YOUR_TAG_HERE>`.

## Building on an M1 Mac

This requires Docker to build EEs.  Podman does not function with these steps on an M1 Mac at this time.

1. Clone this repository.
2. Update the included files in this repository with the variables to include in the EE.
   * `requirements.yml` for Ansible collections and roles.
   * `requirements.txt` for Python dependencies.
3. Run `ansible-builder create` to create the `context` directory.
4. Change into the newly created `context` directory.
5. Open the newly created file `Containerfile`.
6. Add `--platform=linux/amd64` to each `FROM` statement since most quay images are only built for the `linux/amd64` platform.
    * Statements will look like: `FROM --platform=amd64 $EE_BASE_IMAGE as galaxy`
7. Using `docker buildx`, which is capable of building multi-architecture containers, to build an image that can be used on both `AMD64` and `ARM64/aarch64` (M1 and other ARM) CPUs.

```bash
export REGISTRY=quay.io/scottharwell
export VERSION=0.0.1
docker buildx build --platform linux/arm64,linux/amd64 -t $REGISTRY/server-setup-ee:$VERSION -t $REGISTRY/server-setup-ee:latest --push .
```

Once the container is deployed to your container registry, then you can pull the EE down to your M1 Mac natively, or to Ansible Controller running on `amd64` machines.