This is a simple execution environment definition that I created to use to test the Microsoft Azure Ansible collection.

1. Clone this repo
2. Run `ansible-builder build --container-runtime=docker -t <YOUR_TAG_HERE>`

*Note*: If you're on an M1 Mac, then run `ansible-builder create` and the edit the context/Dockerfile so that the EE_BASE_IMAGE uses the `linux/amd64` platform, like the following.  Then, run `docker build` to build directly from the Dockerfile.  You can build multi-platform images compatible with Apple Silicon with `docker buildx`.

```Dockerfile
...
FROM --platform=linux/amd64 $EE_BASE_IMAGE as galaxy
...
```