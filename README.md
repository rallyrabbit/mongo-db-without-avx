# How to Build Mongo DB without AVX Extension Requirements

I built this patchfile from multiple attempts to build Mongo DB for multiple targets where Intel AVX extensions are not present.  Particularly ARM and x86 (Celeron).  This took a lot of trial and error.  The idea is to help you build Mongo in a way that preserves core Mongo behavior with minimal changes while disabling AVX extension requirements.

## What are AVX Exntensions?

This is the Intel Advanced Vector Extensions is designed to help items for workloads, particularly around meth calculations and simulations.  It's used primarily in certain use cases of Mongo DB, but not everyone's use case.

## How to get Mongo

I used v7.x originally and v8.0.4 when I tweaked this.  To get Mongo DB:
* Make directory where you want to work from and go to it
* git clone https://github.com/mongodb/mongo.git
* git checkout r8.0.4 (or latest version that you want to work with)
* git pull

## System Prerequisites

For this writeup I am focusing on ARM/x86 and Linux.

Things that you will need:
* Pyton 3.7 or greater
* apt-get install libssl-dev lld python3.10-venv python3-pip liblzma-dev libcurl4-openssl-dev build-essential git scons python3-dev python3-pip libssl-dev libcurl4-openssl-dev libboost-dev
* apt install python-dev-is-python3 libssl-dev
* python3 -m pip install -r etc/pip/compile-requirements.txt

Installation
* Will need poetry installed locally ( https://python-poetry.org/docs/#installation )
  * sudo apt-get install pip
  * sudo /usr/bin/python3 -m pip install 'poetry==1.5.1'

Update Python Modules
* /usr/bin/python3 -m poetry install --no-root --sync

Other Install Requirements
* sudo pip install mongo-tooling-metrics
* sudo pip install retry
* sudo pip install cheetah3
* sudo apt-get install libssl-dev 

## Patching

What this does:
* Turns off sandybridge experimental optimization
* Turns of SSE3 and SSE4.1 support

To Patch:
* Go to root of mongo source code directory
* git apply < mongo-rem-avx.path

## Building Mongo
To build, run this command from the root mongo source directory
* python3 buildscripts/scons.py install-mongod --disable-warnings-as-errors

## Install

I put this in /usr/local/mongo while some people use /opt/mongo and you need to make sure python poetry is in the path.

To build all
* python3 buildscripts/scons.py install-all-meta DESTDIR=/usr/local/mongo --disable-warnings-as-errors

To build only mongod
* python3 buildscripts/scons.py DESTDIR=/usr/local/mongo install-mongod --disable-warnings-as-errors

## Post Install
Things you will need post install
* Create a mongod.conf file to go into your /etc (sample is in this project, this is a debian/ubuntu sample)
* chown root:root /etc/mongod.conf
* useradd mongodb
* mkdir /var/lib/mongodb
* mkdir /var/log/mongodb
* chown mongodb:mongodb /var/lib/mongodb
* chown mongodb:mongodb /var/log/mongodb
* chmod 750 /var/lib/mongodb
* chmod 750 /var/log/mongodb

## After (what tod do)

After you test it and it works:
* use the "strip" command to remove debug symbols from the executables and reduce the size
* If you have made static executables, use readelf -d <filename> to validate that the static linking worked

# Mongo Tools

My effort was to make a static linked mongo that is easily deployed.  This is a go project located: https://github.com/mongodb/mongo-tools

* Note, mongo-tools cannot be statically linked due to the fact that is links in system variable components.  So this is not possible due to items like getaddrinfo being used and being different system to system.

# Handy Mongo Build things to know

"Clean" after build
* python3 buildscripts/scons.py --clean

Build Static
* python3 buildscripts/scons.py --link-model=static install-servers

Build with a different path target

Different Builds to make
* python3 buildscripts/scons.py DESTDIR=/usr/local/mongo install-servers

Disable Warnings
* python3 buildscripts/scons.py --disable-warnings-as-errors install-servers

Other Build Targets
* Only mongod
  * python3 buildscripts/scons.py install-mongod
* Only mongos
  * python3 buildscripts/scons.py install-mongos
* mongod and mongos
  * python3 buildscripts/scons.py install-core
* All server components (really just mongod, mongos and some scripts)
  * python3 buildscripts/scons.py install-servers
* install-devcore (mongod, mongos, and jstestshell)
  * python3 buildscripts/scons.py install-devcore
* All server, end user pieces, and scripts
  * python3 buildscripts/scons.py install-all
* Everything
  * python3 buildscripts/scons.py install-all-meta
