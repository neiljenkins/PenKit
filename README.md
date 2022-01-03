# Introduction

An outline of the process for beginning a penetration test, identifying the attack surface and typcial tools and methods to try in an initial investiagtion phase.

## Contents

1. [Host discovery and enumaration](#hosts)
2. [SMB shares](#smb)
3. [FTP shares](#ftp)
4. [Database services](#db)
5. [Web servers](#web)
6. [RDP services](#rdp)

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

`nmap -sV -v -O {ip address}`

This will attempt to enumerate the services and operating system that are running on the host.

<a name='smb'>

# SMB shares

</a>

<a name='ftp'>

# FTP shares

</a>

<a name='db'>

# SQL and NoSQL database services

</a>

<a name='web'>

# Web servers

</a>

<a name ='rdp'>

# RDP services

</a>