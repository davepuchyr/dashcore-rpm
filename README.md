Note, if you are looking for the "blessed" binaries (tested and runnable
versions) built from the the source RPM(s) described below, you can find them
here: [Dash Core Wallet for Fedora Linux, RHEL, and CentOS](https://docs.google.com/document/d/18qwFkDKfyZhvecuR5kxiIKmPsjPZjFhRY0EsfHYbD7I).
Please remember to backup your Dash wallet before installing another version.

# Dash Core Source RPMs

**Current stable sources:**

* Fedora 23: `dashcore-0.12.0.58-1.taw.fc23.src.rpm`
* CentOS and RHEL 7: `dashcore-0.12.0.58-1.taw.el7.centos.src.rpm`

**Current testnet/experimental sources:**
*Use test RPMs at your own risk*

* Fedora 24: `dashcore-0.12.1-TESTNET20160920.taw.fc24.src.rpm`
* CentOS and RHEL 7: `dashcore-0.12.1-TESTNET20160920.taw.el7.centos.src.rpm`

**Dash (Digital Cash)** is a privacy-centric digital currency that enables
instant transactions to anyone, anywhere in the world. It uses peer-to-peer
technology to operate with no central authority where managing transactions and
issuing money are carried out collectively by the network. Dash is based on the
Bitcoin software, but it has a two tier network that improves upon it. Dash
allows you to remain anonymous while you make transactions, similar to cash.

Dash is also a platform for innovative decentralized crypto-tech.

More about Dash can be found here: https://dash.org
Dash on github can be found here: https://github.com/dashpay/dash

----

**This repository houses source RPMs for the latest stable (and experimental)
releases of Dash.** Using these SRPMs one should be able to build binary RPMs
for your specific linux variety and architecture.

A source RPM (SRPM or .src.rpm) nicely packages up the source code of a
program/project and contains all the instruction needed to build (compile and
package) corresponding binary RPMs. In the case of Dash, one source RPM will
compile the code and generate all the binary runnable RPMs associated to the
Dash project.

Important notes:

0. There are versions considered **stable** and versions considered
   **experimental**. Versions with an "x" in their version number should be
   considered the most experimental.
0. **Stable** versions have been developed and tested on Fedora 23, RHEL7, and CentOS7, all on the x86_64 architecture. I welcome folks to experiment with other distributions and architectures.

----

#### [0] For RHEL and CentOS, you need to subscribe to special repositories (ignore this step if you are using Fedora)

**RHEL 7 Specific Instructions**

Do this as root (or a sudo'er). Subscribe to all the appropriate repositories and add the EPEL repo. Note, RHEL7 has proven to be a challenging platform to build for. Be aware that CentOS7 RPMs should work just fine on RHEL7.

```
# sudo subscription-manager repos --enable rhel-7-server-rpms
# sudo subscription-manager repos --enable rhel-7-server-extras-rpms
# sudo subscription-manager repos --enable rhel-7-server-optional-rpms
# rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```

**CentOS 7 Specific Instructions**

As root, do this...
```
# yum install epel-release
```


#### [1]  Set up your Build environment

RPM? How? What?: https://fedoraproject.org/wiki/How_to_create_an_RPM_package (read especially "Preparing your system")

In order to build from a source RPM, you first need to set up your environment. If you have not already, do this as your normal user (not root) from the commandline:

```
# For RHEL and CentOS, it's the same, just don't include the "fedora-packager"
sudo dnf install @development-tools fedora-packager rpmdevtools rpmdev-setuptree
```

That will set up a working folder tree at `~/rpmbuild/`

_If that fails,_ you need to read more about setting up your development
environment at the link that was provided earlier.

Note, it suggests using a separate user on your system to build RPMs… you can
do that, but for our examples, I am assuming you are doing it with whatever
user you want. I execute these commands from my personal normal user account
usually.


#### [2] Download a source RPM of your choosing

For example, at the time of this writing the latest Dash src.rpm of version
`0.12.0.58`, is considered "stable". For the purposes of this document, we are
going use version-release `0.12.0.58-1.taw` as our example. Download the one
specific to your linux distribution Fedora, RHEL, or CentOS. If you are
attempting to build in a different environment, download the source RPM that as
closely matches your platform and experiment away.

#### [3] Verify the RPM has not been tampered with

This is done in one of two ways: (1) with an sha256sum hash check and (2) with
a GPG signature check. Assuming the source RPM is digitally signed, GPG
signature verification is vastly more secure, but more complicated.

**Verification via sha256sum**

*Note, at the end of this document, a list of the source RPMs and the
sha256sums that they should match, are listed.*

From the commandline...

    $ sha256sum dashcore-0.12.0.58-1.taw.*.src.rpm

The result will be something like this and it needs to match the posted hash
_(note, this example hash may be incorrect)_.

`
f12edc5c22bb4bdeeb7d493de17bc8c703d2592838ddd292eff3c884d3a93a09  dashcore-0.12.0.58-1.taw.fc23.src.rpm
d0ca8947bc71785ccac7a0f80f526b886e36d8efafa7636f4e2433fb4b53bb3b  dashcore-0.12.0.58-1.taw.el7.centos.src.rpm
`

**Verification of the source RPMs digital signature**

If I built these packages correctly, their appropriate sha256 hash should be
posted below _and_ they should pass a gpg digital signature check signed by my
public key.

(1) Import my GPG key into your RPM keyring if you have not already done so.
You have to do this as the root user.

    $ sudo rpm --import https://raw.githubusercontent.com/taw00/public-keys/master/taw-694673ED-public-2030-01-04.asc

Or navigate to http://github.com/taw00/public-keys and fetch the key manually
and import it locally.

(2) Check the signature

    $ rpm --checksig dashcore-0.12.0.58-1.taw.*.src.rpm

You should see something like: `dashcore-0.12.0.58-1.taw.fc23.src.rpm: rsa sha1 (md5) pgp md5 OK`

If the package is not signed, or if the result is something less substantial,
like just `sha1 md5 OK`, or no signature. Then, I would not recommend
installing the source RPM. If it says
`(MISSING KEYS: RSA#694673ed (MD5) PGP#694673ed)`,
you did not successfully import my key in step 1.


#### [4] Install the source RPM

Again, from the command line as a normal user... First, move that source RPM into the source RPMs location in the rpmbuild build tree:

    $ mv dashcore-*.src.rpm ~/rpmbuild/SRPMS/
    $ # Install the sucker:
    $ rpm -ivh ~/rpmbuild/SRPMS/dashcore-0.12.0.58-1.taw.fc23.src.rpm #Or whatever version you are installing.

That should explode source code and patch instruction into
~/rpmbuild/SOURCES/ and the build instructions into ~/rpmbuild/SPECS/.
Something likes this...

```
~/rpmbuild/SPECS/dashcore-0.12.0.58.spec
~/rpmbuild/SOURCES/dash-0.12.0.58.tar.gz
~/rpmbuild/SOURCES/dashcore-0.12.0.58-contrib-fedora.tar.gz
~/rpmbuild/SOURCES/dashcore-0.12.0.58-dashify.tar.gz
~/rpmbuild/SOURCES/dashcore-0.12.0.58-fedora.patch
```

#### [5] Build the binaries

One source RPM will often build multiple binary RPMs. In this case, six RPMs
are built (with the debuginfo RPM optionally built). See them listed later
in this document.

Actually building the binary RPMs is as easy as running the rpmbuild command
against a specfile. For example:

    $ cd ~/rpmbuild/SPECS
    $ rpmbuild -ba dashcore-0.12.0.58.spec

Note, you may run into a failed build. Look at the BuildRequires in the .spec
file. For example, you may have to install a few RPMs first, like gcc-c++ and
and others. Or just note the output of the failed build and add the packages.
For example, I had to do something like this for a RHEL7 build...

    $ # Example only!
    $ sudo yum install qrencode-devel miniupnpc-devel protobuf-devel openssl-devel boost-devel libdb4-cxx-devel libevent-devel qt5-qtbase-devel qt5-linguist


If all goes well, the build process may take 30+ minutes and nicely bog down
your computer. If the build succeeded, the build process will list the RPMS
that were created at the end of the terminal window output. The binary RPMs
will be saved in the _~/rpmbuild/RPMS/_ directory and a newly minted source RPM
will land in the _~/rpmbuild/SRPMS/_ directory. The binary RPMs will be
these...

* **dashcore-client** -- The dash-qt wallet and full node _(note, some older
  sources merely named this package "dash" and not "dashcore-client")_
* **dashcore-utils** -- dash-cli, a utility to communicate with and control a
  Dash server via its RPC protocol, and dash-tx, a utility to create custom
  Dash transactions.
* **dashcore-server** -- dashd, a peer-to-peer node and wallet server. It is
  the command line installation without a GUI.  It can be used as a commandline
  wallet and is typically used to run a Dash Masternode. Requires
  dashcore-utils to be installed as well.
* **dashcore-libs** -- provides libbitcoinconsensus, which is used by third
  party applications to verify scripts (and other functionality in the future).
* **dashcore-devel** -- provides the libraries and header files necessary to
  compile programs which use libbitcoinconsensus. Requires dashcore-libs to be
  installed as well.
* **dashcore-[version info].src.rpm** -- The source code -- the source RPM, or
  SRPM You want to build binaries for your RPM-based linux distribution? Use
  this source RPM to do so easily.
* **dashcore-debuginfo** -- debug information for package dash. Debug
  information is useful when developing applications that use this package or
  when debugging this package.  (99.999% of you do not need to install this)


***Are you impatient?*** Binaries for Dash have already been built for these
linux distributions.

* stable: https://drive.google.com/open?id=0B0BT-eTEFVLORG03ck1tem1idEE
* test: https://drive.google.com/open?id=0B0BT-eTEFVLOcm12Zzcza2psTzQ

----

### The sha256 verification hashes:

```
6960916334de35ddf8d96d87dcb9a188a095d430ada13c068286e483db4edf32  dashcore-0.12.0.56-7.taw.fc23.src.rpm

f12edc5c22bb4bdeeb7d493de17bc8c703d2592838ddd292eff3c884d3a93a09  dashcore-0.12.0.58-1.taw.fc23.src.rpm
d0ca8947bc71785ccac7a0f80f526b886e36d8efafa7636f4e2433fb4b53bb3b  dashcore-0.12.0.58-1.taw.el7.centos.src.rpm

ae6aa089c8cab4e0979d89e9a76e1a5d6dfd738ef6eed4de96517f801f7579e5  dashcore-0.12.1-TESTNET20160920.taw.el7.centos.src.rpm
06f3ed6d06a3326d99b4801551ae93e56b3bc9f3e2c22122f6e368b2655fab5b  dashcore-0.12.1-TESTNET20160920.taw.fc24.src.rpm
```
----

### Advanced: Creating your own tagged builds

If you are feeling a bit froggy, build and compile the binary RPMs specific to
your name/initials.

Let's say your name is Barney Miller (initials "bm").

```
cp dashcore-0.12.0.58.spec dashcore-0.12.58-1.bm.spec
```

* Edit `dashcore-0.12.0.58-1.bm.spec`
* Change the _bumptag_ value in that file from '.taw' to '.bm'
* Add a stanza in the changelog at the bottom reflective of what you did (copy
  the format)...

...and build your RPMs from that...


```
rpmbuild -ba dashcore-0.12.0.58-1.bm.spec
```

If all goes to plan, in 30 or 40 minutes you should have a set of binary
packages, specifically built to your system with a release of '7.bm'.

If there is a significant problem where you have to, for example, fix the
`configure.ac` file in the `dash-0.12.0.58.tar.gz` archive (common issue)... you
will have to do something like this:

* Copy the archive to some working director and then extract it...

      tar xvzf dash-0.12.0.58.tar.gz

* Copy the resultant (the original pristine) folder…

      cp -a dash-0.12.0.58 dash-0.12.0.58.orig --

* Work on the `dash-0.12.0.58/configure.ac` file, build a new patch, and try to
  rebuild things (iterate iterate iterate). All that is a bit beyond this
  document, but this is a good place to start to understand RPMs:
  https://fedoraproject.org/wiki/How_to_create_an_RPM_package

Finally, once built and you are happy, instead of relying on sha256sum hash
verification, you can GPG sign your packages. For instruction, read these nice
summaries (hint, you will have to install the `rpm-sign` RPM first):

* http://fedoranews.org/tchung/gpg/
* http://blog.packagecloud.io/eng/2014/11/24/howto-gpg-sign-verify-rpm-packages-yum-repositories/

And if you are feeling really ambitious, you can set up a publicly facing dnf
(once called yum) repository and making your packages available through
super-automated means to the world (keep reading the Fedora documentation for
more information about yum and dnf).

## Good Luck

That should get you started! Good luck! -todd _(dagrarian on dashtalk and
slack, taw, taw00, taw0<n> in various other venues)_

...

_Special thanks go out to [Michael
Hampton](https://www.ringingliberty.com/bitcoin/) for his mature bitcoin spec
file from which I originally templated these dashcore spec files._
