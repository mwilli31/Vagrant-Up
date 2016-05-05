# Vagrant Up

Configure a vagrantfile through a yaml file.

![Vagrant up Logo](http://i.imgur.com/CdNhaRN.png)

## Prerequisites

You'll need to have the following prerequisites **installed** on your workstation:

* [VirtualBox](https://www.virtualbox.org/)
* [Vagrant](http://www.vagrantup.com/)

* Add an appropriate vagrant box (optional)
```bash
    $ vagrant box add ubuntu/trusty64
```

## Quick Start


```bash
  $ git clone git@github.com:Mayccoll/Vagrant-Up.git
  $ cd Vagrant-Up
  $ vagrant up
```

## Files

```
.
├── CONFIG.yaml
├── README
├── share
├── .editorconfig
└── Vagrantfile

```

## Configuration

There are one configuration files ```CONFIG.yml```

```bash
.
└── CONFIG.yml

```

#### Config VirtualBox VM

  > **./CONFIG.yaml**

```yaml
- name               : vag-wordpress
  box                : ubuntu/trusty64
  box_check_update   : false
  ram                : 1028
  cpus               : 1
```

#### Defining a Forwarded Port

  > **./CONFIG.yaml**

```yaml
ports           :
  - guest       : 3000
    host        : 3000

  - guest       : 80
    host        : 8080
```

#### Config Synced folders

  > **./CONFIG.yaml**

```yaml
  syncDir         :
    - host        : share
      guest       : /home/vagrant/share

    - host        : www
      guest       : /home/vagrant/www
      owner       : vagrant
      group       : vagrant
      dmode       : 775
      fmode       : 775
```
