# Image Environment Base Ubuntu 24.04 LTS

Ubuntu 24.04 LTS (Noble Numbat) base image for Kaptain environment definition
builds. Provides kubectl, yq 4, and common debugging tools in a minimal Ubuntu
environment with a non-root `kaptain` user and standard directory layout for
deployment scripts and manifests.


## Tagging

Images are tagged to match the bundled kubectl version (e.g., kubectl `1.32.11`
results in 1.32.1, 1.32.2, etc). The patch version component is unique but
because the patch version doesn't really matter much, we don't need to bother
using 4 version sections which is an option to do and sometimes makes sense.
Due to the kubectl +/- 1 version compatibility allowance this makes it easy to
see if the image is compatible with your current cluster version range (ideally
just one version across the lot unless mid-upgrade).


## See Also

- [image-environment-base-alpine-3-23](../image-environment-base-alpine-3-23) - Alpine variant
- [image-environment-base-trixie-slim](../image-environment-base-trixie-slim) - Debian Trixie variant
- [image-environment-base-ubi-9-minimal](../image-environment-base-ubi-9-minimal) - Red Hat UBI variant
