# Dash for Fedora, CentOS, and Red Hat Enterprise Linux<br />_...wallet, node, and masternode_

This document describes what is available for Fedora, CentOS, and Red Hat
Enterprise Linux (RHEL) users seeking natively compiled Dash Core software. This
document will also guide those users through the process of configuring their
systems to easily install, verify, and update that software. Once configured,
updating Dash Core software will be just as trivial as updating the rest of
their system.

> What is Dash? [dash.org](https://dash.org/), [official documentation](https://dashpay.atlassian.net/wiki/display/DOC/Official+Documentation)<br />
> What is a Masternode? <https://dashpay.atlassian.net/wiki/display/DOC/Masternode><br />
> What are these Linuxes? [Fedora](https://getfedora.org/), [CentOS](https://www.centos.org/), [RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux)
 
If you know your way around yum, dnf, and Dash Core, I could reduce this document to two lines...<br />
**dashcore-fedora.repo:** <https://github.com/taw00/dashcore-rpm/blob/master/dashcore-fedora.repo><br />
**dashcore-centos.repo:** <https://github.com/taw00/dashcore-rpm/blob/master/dashcore-centos.repo><br />
Otherwise, keep reading.

## *"TL;DR ...I just want to install the Dash Core GUI Wallet!"*

Proper packaging and repositories make installation trivial.

Assuming you are logging in as a normal user who has `sudo` priviledges.<br />
At the terminal command line...

```
# My system is Fedora...
cd /etc/yum.repos.d/
sudo curl -O https://raw.githubusercontent.com/taw00/dashcore-rpm/master/dashcore-fedora.repo
cd -
sudo dnf install -y dashcore-client --refresh
dash-qt
```

...or if you are running CentOS or RHEL...

```
# My system is CentOS or RHEL
cd /etc/yum.repos.d/
sudo curl -O https://raw.githubusercontent.com/taw00/dashcore-rpm/master/dashcore-centos.repo
cd -
sudo yum install -y dashcore-client
dash-qt
```

**Boom! Done!** You should now see a Dash GUI Wallet open up on your screen and
reference to it in your desktop menus.

## *"TL;DR ...I want to install a Dash Masternode!"*

That takes some explanation. Start here:
<https://github.com/taw00/dashcore-rpm/tree/master/documentation>

&nbsp;

---

## Additional detail...

To automate installation, update, all you have do to do is download and copy
the appropriate `dashcore-*.repo` file into your package manager configuration
directory `/etc/yum.repos.d` and you will be ready to rock-and-roll with the
Dash software as demonstrated with the instruction above.

If you are interested in building your own RPM packages from source, those can
be found here: <https://github.com/taw00/dashcore-rpm/blob/master/README.source.md>

If you are looking for Dash wallets for other platforms, those can be found here:
<https://www.dash.org/downloads/>

**Masternode Documentation:** <https://github.com/taw00/dashcore-rpm/tree/master/documentation><br />   
**Other Documentation:** <https://dashpay.atlassian.net/wiki/pages/><br />
**Other Masternode Documentation:** <https://dashpay.atlassian.net/wiki/display/DOC/Masternode>


## Configuring your system to have access to the Dash RPM package repositories

We are going to do this at the command line from an account that has `sudo`ers
access. Log into a terminal and do this...

*...if using Fedora:*

```
cd /etc/yum.repos.d/
sudo curl -O https://raw.githubusercontent.com/taw00/dashcore-rpm/master/dashcore-fedora.repo
cd -
```

*...if using CentOS or RHEL*

```
cd /etc/yum.repos.d/
sudo curl -O https://raw.githubusercontent.com/taw00/dashcore-rpm/master/dashcore-centos.repo
cd -
```

That's it! You are now configured to install Dash Core through your package
manager.

## List what's available

Want to see what packages are available? Do this...<br />

```
# Fedora
sudo dnf list|grep dashcore
```

...or...

```
# CentOS or RHEL
sudo yum list|grep dashcore
```

You should get a nice listing of all the packages available to your linux
system.

## Stable versus the not-so-stable testing repos

By default, those `*.repo` files configure your system to only pull from the
"stable" repository.

NOT RECOMMENDED: If you want to install the "testing" software instead, do this:<br />

*...fedora:*

```
sudo dnf config-manager --set-disabled dashcore-stable
sudo dnf config-manager --set-enabled dashcore-testing
sudo dnf list --refresh|grep dashcore
```

*...centos or rhel:*

```
sudo yum-config-manager --disable dashcore-stable
sudo yum-config-manager --enable dashcore-testing
sudo yum list|grep dashcore
```

The listing command at the end isn't required, but it is helpful to show that
the version of what software is available just changed.

> Critical: If you are just testing things, remember to enable testnet in your
`dash.conf` file. If testnet, ensure you have `testnet=1` set.

To switch back to the stable repositories, just flip-flop the enable/disable
command line switches above and re-run those commands.

## Installing or Upgrading Dash Core software

If you have configured things appropriately. Installation is truly this easy...

***To install the non-graphical, command-line-only, version of the software:
`dashd`***

*...fedora:*

```
sudo dnf install dashcore-server
```

*...centos or RHEL:*

```
sudo yum install dashcore-server
```

The operating system's package manager will automagically pull in any
dependencies, give you a list, and ask you if you are sure. You will see that
for `dashcore-server`, `dashcore-utils` will also be installed. It's a needed
dependency because it contains the `dash-cli` command line utility.

You want the GUI wallet instead? Say "N"o to that installation request, run the
same command as above, but choose `dashcore-client` instead. YUM and DNF also
automatically check to see if they packages are digitally signed appropriately
and if the files are corrupt. Fancy! If you are familiar with `apt-get`, it's
more or less the same thing if someone built similar packages for Ubuntu.

**Future upgrades...**

Let's say you see in the [Dash Forums](https://dash.org/forum) that a new version of Dash
Core was released. You want your system updated as soon as possible. It's now
trivial. Just do this...

*...fedora:*

```
sudo dnf upgrade
```

*...centos or RHEL:*

```
sudo yum update -y
```

And then after the refreshed packages were updated, restart the wallet or
`dashd`. Please note that for major upgrades, more actions may have to be
taken.


#### Good luck!

Send commentes or feedback to <t0dd@protonmail.com>

&nbsp;

&nbsp;

&nbsp;

---

## Appendix: QnA and other information

#### Q: I see CentOS packages but nothing *specific* to RHEL, why is that?

A: Because CentOS packages will run just fine on RHEL.

#### Q: Sentinel? What's this Sentinel thing?

A: Operating a Masternode requires 3 things now...

1. A collateral-bearing wallet
2. A `dashd` server node
3. Sentinel

Sentinel is going to increasingly take over the duties of a Masternode, whereas
`dashd` will perform the normal node operations. Both are required.

This is all explained in the [Masternode
documentation](https://github.com/taw00/dashcore-rpm/tree/master/documentation).


#### Q: Testing?

A. In that repository configaration file that you downloaded and installed in
directory `/etc/yum.repos.d`, you have access to test versions of Dash. Unless
you know what you are doing, I do not recommend you enable that repository.


#### Q: What are all these packages?

* **dashcore-client** -- The dash-qt wallet and full node.

* **dashcore-utils** -- dash-cli, a utility to communicate with and control a
  Dash server via its RPC protocol, and dash-tx, a utility to create custom Dash
  transactions.

* **dashcore-server** -- dashd, a peer-to-peer node and wallet server. It is the
  command line installation without a GUI.  It can be used as a commandline
  wallet but is typically used to run a Dash Masternode (runs ideally as a
  serviced daemon). *Requires `dashcore-utils` to be installed as well.*

* **dashcore-libs** -- provides libbitcoinconsensus, which is used by third
  party applications to verify scripts (and other functionality in the future).

* **dashcore-devel** -- provides the libraries and header files necessary to
  compile programs which use libbitcoinconsensus. *Requires `dashcore-libs` to
  be installed as well.*

* **dashcore-[*version info*].src.rpm** -- The source code -- the source RPM, or
  SRPM. You want to build binaries for your RPM-based linux distribution? Use
  this source RPM to do so easily.

* **dashcore-debuginfo** -- debug information for package dash. Debug
  information is useful when developing applications that use this package or
  when debugging this package. (*99.999% of you do not need to install this*)


---

#### Good luck!

Got a dash of feedback? Send it my way: <https://keybase.io/toddwarner>
And of course, donations welcome: [XyxQq4qgp9B53QWQgSqSxJb4xddhzk5Zhh](dash:XyxQq4qgp9B53QWQgSqSxJb4xddhzk5Zhh)

