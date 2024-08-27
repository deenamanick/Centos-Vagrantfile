# Vagrantfile For CentOs 7

Vagrant is a tool for building and managing virtual machine environments in a single workflow. In short, Vagrant tools will make life easier for developer who wants to setup a virtual machine in a short period. 
As part of DevOps in Depth Course structure, we are building Cento7 in Virutal Maching using Vagrant tool.

## Features

- Easy to Setup Virtual Machine Faster
- Building and Managing Development environment Easily- 
- Easy to destroy Virtaul Machine once development is completed

## Plugins

In order to use this Vagrantfile, You need to install [Landrush](https://github.com/vagrant-landrush/landrush) & [hostmanager](https://github.com/devopsgroup-io/vagrant-hostmanager) as a pre-requisite packages.

Install pre-requistie dependencie Packages.

```sh
vagrant plugin install landrush
vagrant plugin install hostmanager
```

Create a Directory:

```sh
mkdir centos7-vagrant
```

cd to directory:

```sh
cd centos7-vagrant
```

Create or Copy Vagrantfile:

```sh
cp Vagrantfile .
```
Run Vagrant Up command:

```sh
vagrant up
```
To know more about Vagrant
 [Vagrant in action]: <https://www.jeeviacademy.com>

## License

MIT


