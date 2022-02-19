# Introduction

An outline of the process for beginning a penetration test, identifying the attack surface and typcial tools and methods to try in an initial investiagtion phase.

## Contents

1. [Host discovery and enumaration](#hosts)
2. [SMB shares](#smb)
3. [FTP shares](#ftp)
4. [Database services](#db)
5. [Web servers](#web)
    - [Directory mapping](#dir)
    - [Burp basics](#burp)
    - [Fuzzing](#fuzz)
6. [RDP services](#rdp)
7. [Nmap scripts](#nse)
8. [Wireless](#wireless)
9. [Hash cracking](#hashes)

<a name='hosts'>

# Host disovery and initial enumeration

</a>

## Nmap

### Host disovery

Host identification can be done by scanning a network 

`nmap {ip address or range}`

It may be that the host has been set up to not respond to pings, in which case we can use

`nmap -Pn {ip adderess or range}`

This will skip the ping scan and treat all hosts as online.

Adding the `-p-` option will scan all ports on a host rather than just the 1000 common ports that Nmap scans as standard.

### OS and service enumeration

Once hosts to target have been identified, we can use more detailed scans to identify services and operating system with

`nmap -sV -v -O -p- {ip address}`

This will attempt to enumerate the services and operating system that are running on the host.

<a name='smb'>

# SMB shares

</a>

## Tools

- smbclient
- enum4linux
- smbmap

## Enumerating available shares and accessing public files with smbclient

We can use smbclient to list the shares that are available on a host with

`smbclient -L host -N`

If a public share is found then we can access it with

`smbclient \\\\hostip\\sharename`

This will give us an interactive SMB shell that we can then use to browse the share and `get` files.

`get file -` will output the contents to stdout rather than transferring the file if we want to have behaviour more like `cat`.

## Further enumeration with enum4linux

enum4linux can potentially give us much more information such as users and can also use null sessions to gain further access. Null sessions are disabled by default on recent implementations but may still be configured on legacy systems.

`enum4linux host`

We can also brute force share names to check for non-discoverable public shares with

`enum4linux -s sharenameFile host`

<a name='ftp'>

# FTP shares

</a>

## Anonymous shares

If an FTP share has been set up to allow anonymous access then we can log in using the usename 'anonymous'.

<a name='db'>

# SQL and NoSQL database services

</a>

<a name='web'>

# Web servers

</a>

<a name='#dir'>

## Directory mapping

</a>

<a name='#burp'>

## Burpsuite basics

</a>

<a name='#fuzz'>

## Fuzzing

</a>


<a name ='rdp'>

# RDP services

</a>

<a name ='nse'>

# Nmap scripts

</a>

<a name='nmap-smb'>

## SMB

</a>

`--script='smb-enum-shares` - Attempts to get information about the shares on the target

`--script='smb-brute` - Allows for brute forcing of SMB credentials, storing the credetials for use by other SMB scripts that are run at the same time

<a name='nmap-ftp'>

## FTP

</a>

`--script='ftp-brute` - Allows for brute forcing of FTP credentials

<a name='nmap-ssh'>

## SSH

</a>

`--script='ssh-brute` - Allows for brute forcing of ssh credentials

<a name='wireless'>

# Wireless

</a>

## Network identification and packet capture

In order to capture network traffic, need a network card that can operate in monitor or promiscuous mode. This allows for the capture of packets that are being broadcast and aren't addressed to the attacking machine.

In order to enable monitor mode, can use:

`airmon-ng check kill` to check for processes that may interfere with putting the adapter into monitor mode.

`airmon start wlan0` will put the wireless card on interface wlan0 into monitor mode.

`airmon stop wlan0` will return the adapter on interface wlan0 to managed mode for connecting to an access point.

Once the adapter is in monitor mode, then we can use Wireshark to capture packets on the interface.

<a name='hashes'>

# Hash cracking

</a>