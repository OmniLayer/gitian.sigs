Gitian results and signatures for Omni Core
-------------------------------------------

Release binaries for Omni Core are deterministically build via Gitian.

  https://gitian.org

To compare build results of maintainers, and contributors, `gsign`, which is part of Gitian, is used to create a "fingerprint" of the binaries, as well as the dependencies used during the build process.

The "fingerprints" are cryptographically signed via GPG and published to this repository.

This process ensures that binaries, and the tool chain, were not tampered with and that the same source was used. It allows any user to verify releases by building binaries locally, and comparing the results.

#### Documentation

- [Deterministic building of Omni Core](https://github.com/OmniLayer/omnicore/blob/omnicore-0.0.10/doc/gitian-building.md)
- [Signing of Omni Core build results via Gitian](CONTRIBUTING.md)
