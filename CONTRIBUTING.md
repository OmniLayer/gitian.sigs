Signing of Omni Core build results via Gitian
---------------------------------------------

- [1. Prepare repository](#1-prepare-repository)
	- [1.1 Fork gitian.sigs repository](#11-fork-gitiansigs-repository)
- [2. Prepare environment](#2-prepare-environment)
	- [2.1 Clone gitian.sigs](#21-clone-gitiansigs)
	- [2.2 Import your GPG key](#22-import-your-gpg-key)
- [3. Build Omni Core](#3-build-omni-core)
	- [3.1 Define signing identity](#31-define-signing-identity)
	- [3.2 Define release base string](#32-define-release-base-string)
	- [3.3 Sign Linux build results](#33-sign-linux-build-results)
	- [3.4 Sign Windows build results](#34-sign-windows-build-results)
	- [3.5 Sign OS X build results](#35-sign-os-x-build-results)
- [4. Publish signatures](#4-publish-signatures)
	- [4.1 Commit results and signatures](#41-commit-results-and-signatures)
	- [4.2 Push to your fork of gitian.sigs](#42-push-to-your-fork-of-gitiansigs)
	- [4.3 Create pull request for upstream repository](#43-create-pull-request-for-upstream-repository)

### 1. Prepare repository

#### 1.1 Fork `gitian.sigs` repository

We assume the user's GitHub name is `OmniDev <odev@interwebs.com>`.

Fork the upstream repository via github.com:
```
https://github.com/OmniLayer/gitian.sigs#fork-destination-box
```

### 2. Prepare environment

#### 2.1 Clone `gitian.sigs`

In the Gitian building environment:
```bash
~$ git clone https://github.com/OmniDev/gitian.sigs.git
```

#### 2.2 Import your GPG key

If not already done before, import your GPG key in the Gitian building environment. Assuming the GPG private key is stored as `private.key`, then:
```bash
~$ gpg --allow-secret-key-import --import private.key
```

### 3. Build Omni Core

#### 3.1 Define signing identity

The signing identity will be used to map build results to users. It is not strictly related to the GitHub username, or the GPG identity, but ideally the GitHub username is used as signing identify. To define:
```bash
~/gitian-builder$ export SIGNER=OmniDev
```

#### 3.2 Define release base string

The release string is used to create a subdir in `../gitian.sigs`. If you're building a tagged release, then `${VERSION}` may already be defined. If you're building a release based on a commit, then there may be `${COMMIT}`.

For tagged releases use the tag as release base string. If `${VERSION}` is defined:
```bash
~/gitian-builder$ export RELEASE=${VERSION}
```
If `${VERSION}` is not defined, and assuming the release tag is `0.0.10.0-rc1`, then:
```bash
~/gitian-builder$ export RELEASE=0.0.10.0-rc1
```

For untagged releases, which are build based on a specific commit, use the base version, followed by the first 10 characters of the commit. Assuming the base version is `0.0.10.0`, and first 10 characters are `f95531ffec`, then:
```bash
~/gitian-builder$ export RELEASE=0.0.10.0-f95531ffec
```

#### 3.3 Sign Linux build results

After building Omni Core for Linux via `./bin/gbuild`, but before switching to the next build target:
```bash
~/gitian-builder$ ./bin/gsign --signer ${SIGNER} --release ${RELEASE}-linux --destination ../gitian.sigs/ ../omnicore/contrib/gitian-descriptors/gitian-linux.yml
```

#### 3.4 Sign Windows build results

After building Omni Core for Windows via `./bin/gbuild`:
```bash
~/gitian-builder$ ./bin/gsign --signer ${SIGNER} --release ${RELEASE}-win --destination ../gitian.sigs/ ../omnicore/contrib/gitian-descriptors/gitian-win.yml
```

#### 3.5 Sign OS X build results

After building Omni Core for OS X via `./bin/gbuild`:
```bash
~/gitian-builder$ ./bin/gsign --signer ${SIGNER} --release ${RELEASE}-osx-unsigned --destination ../gitian.sigs/ ../omnicore/contrib/gitian-descriptors/gitian-osx.yml
```

### 4. Publish signatures

#### 4.1 Commit results and signatures

It is not required to stay in the Gitian environment, and build results may as well be transferred to the host machine, before continuing with the following steps.

If the Gitian environment is used, and if not done earlier, then set your GitHub username and email address, and optionally a GPG signing key:
```bash
~/$ git config --global user.name "OmniDev"
~/$ git config --global user.email odev@interwebs.com
~/$ git config --global user.signingkey ABCDEF01 # optional
```

If the steps are done on the host machine (or somewhere else), redefine, or replace `${RELEASE}` with the actual release tag (e.g. `0.0.10.0-rc1`), and likewise the signing identity `${SIGNER}` with the actual name of the signer (e.g. `OmniDev`).

Navigate to the `gitian.sigs` repository, create a new branch, and add the results via `git`:
```bash
~/gitian.sigs$ git checkout -b ${RELEASE}
~/gitian.sigs$ git add ${RELEASE}-linux/
~/gitian.sigs$ git add ${RELEASE}-win/
~/gitian.sigs$ git add ${RELEASE}-osx-unsigned/
```

Commit the changes, and state the release/tag, as well as the signing entity in the commit message:
```bash
~/gitian.sigs$ git commit -m "${RELEASE} build signatures of ${SIGNER}"
# or:        $ git commit -m "0.0.10.0-rc1 build signatures of OmniDev"
# or:        $ git commit -S
```

#### 4.2 Push to your fork of `gitian.sigs`

Assuming your `git remote` is `origin`, then:
```bash
~/gitian.sigs$ git push origin ${RELEASE}
```

If another branch, other than `${RELEASE}`, was used, then replace the branch name accordingly.

#### 4.3 Create pull request for upstream repository

To publish the results, create a pull request for the `master` branch via github.com for:
```
https://github.com/OmniLayer/gitian.sigs
```
