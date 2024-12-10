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

## Patching

TBD - Fill in

## Building Mongo
To build, run this command from the root mongo source directory
* python3 buildscripts/scons.py install-mongod --disable-warnings-as-errors

## 




