Vagrant VM for CS162
====================

### Running on Vagrant

1. Download the source code and run `vagrant up` inside the root of the project
   directory.
1. The Vagrantfile specifies `ubuntu/trusty64` as the base box for this VM, so
   Vagrant will download that box from the Internet, which may take some time.
1. Once the download is complete, Vagrant will import the VM appliance and run
   our Puppet provisioner.

This provisioner is the only modification we've made to the base box, which
means that you **should** be able to run it on any Ubuntu 14.04 machine.

### Running on your own infrastructure

Vagrant+Virtualbox is the recommended way to run the VM. If you don't have VT-x,
or you would prefer to use your own infrastructure, you can run the provisioner
manually.

#### 1. Set up a Ubuntu 14.04 VM

You can set this up on AWS, DigitalOcean, your home server, etc. Anything that
runs this operating system is okay. Both 64-bit and 32-bit versions are okay.

#### 2. Log in to your VM as root, and download the source code for this project.

If you can't log in as root, just type `sudo su` when you log in, and you will
become root (assuming you have sudo permissions).

The easiest way to download the source code is through a git clone. You may need
to install git first, so:

```shell
$ apt-get install git
$ git clone https://github.com/Berkeley-CS162/vagrant.git
```

#### 3. Add the vagrant user (even if you aren't using vagrant)

There will be some things that are installed for the vagrant user and some
things that are placed in the vagrant user's home directory, so regardless of
whether you are using vagrant, you will need a user named `vagrant` with a home
directory at `/home/vagrant` for this to work.

You should use the `adduser` utility script to do this, NOT `useradd` (unless
you are already familiar with the command-line switches of useradd).

```shell
$ adduser vagrant
```

Make sure your password isn't easily guessable! The personal information section
doesn't matter.

#### 4. Give password-less sudo access to the vagrant user (not optional)

You will need to edit `/etc/sudoers` to give `vagrant` password-less sudo
access. This just means you need to add one line to the end of `/etc/sudoers`.
Open up `/etc/sudoers` with your favorite command-line text editor and add this
line:

```
vagrant ALL=(ALL) NOPASSWD: ALL
```

#### 5. Install puppet using the package manager

You will need to install Puppet. This is as easy as:

```shell
apt-get install puppet
```

#### 6. Run the provisioner

Make sure you are in the project root, and then just run the provisioner.

```shell
$ cd path/to/project/vagrant/
$ puppet apply manifests/site.pp --modulepath modules/
```

#### 7. Log in as the vagrant user (not optional)

You need to actually log in as the `vagrant` user now, since the .bashrc has
`~/.bin` in the PATH for the vagrant user, which has some nifty utilities. You
can do this by logging in with ssh, or if you are already logged in with root,
just run:

```
$ su vagrant
```

You're done! You can use this box just the same as you would a vagrant box. Just
remember to log in with the vagrant user when you're developing code.

Instead of shared folders, you'll need to scp your code from your laptop to the
VM, which is a bit of a pain. To make this easier, check out rsync (it's like
scp with more features) and fswatch (OS X) or inotifytools (Ubuntu). Windows
WinSCP should have an auto-sync feature too.

### Building the distributable box

The `dist/` directory has components to build the final box file. I'm trying a
thing where the box image and components are all from the ubuntu/trusty64 box,
and we just put a puppet provisioner on top of it. To build:

    vagrant box add ubuntu/trusty64
    cd dist/
    ./make.sh

