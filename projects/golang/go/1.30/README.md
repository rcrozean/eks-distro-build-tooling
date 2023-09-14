# EKS Golang 1.30

Current Release: `0`

Tracking Tag: `1.30.0`

Artifacts: https://distro.eks.amazonaws.com/golang-go1.30/release/0/RPMS 

### ARM64 Builds
[![Build status](https://prow.eks.amazonaws.com/badge.svg?jobs=golang-1.30-ARM64-PROD-tooling-postsubmit)](https://prow.eks.amazonaws.com/?repo=aws%2Feks-distro-build-tooling&type=postsubmit)

### AMD64 Builds
[![Build status](https://prow.eks.amazonaws.com/badge.svg?jobs=golang-1.30-tooling-postsubmit)](https://prow.eks.amazonaws.com/?repo=aws%2Feks-distro-build-tooling&type=postsubmit)

### Patches
The patches in `./patches` include relevant utility fixes for go `1.30`.

### Spec
The RPM spec file in `./rpmbuild/SPECS` is sourced from the go 1.30 SRPM available on Fedora, and modified to include the relevant patches and build the `1.30` source."
