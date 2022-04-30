# Introduction

An outline of the process for beginning a penetration test, identifying the attack surface and typcial tools and methods to try in an initial investiagtion phase.

## Contents

1. [Pen Test Checklist](#checklist)
2. [Host discovery and enumaration](#hosts)
3. [SMB shares](#smb)
4. [FTP shares](#ftp)
5. [Database services](#db)
6. [Web servers](#web)
    - [Directory mapping](#dir)
    - [Burp basics](#burp)
    - [Fuzzing](#fuzz)
7. [RDP services](#rdp)
8. [Nmap scripts](#nse)
9. [Wireless](#wireless)
10. [Hash cracking](#hashes)

<a name='checklist'>

# Pen Test Checklist

Remember - Try and have scans / enumerations running in the background.
Run tools like nmap in verbose mode so that early results can give somewhere to start even if the scans take a long time to complete.

## Passive Recon

Not really relevant to the exam

## Active Recon

### Host discovery

* Run scan to identify hosts that appear to be "up"
* Do hosts respond to Ping requests?
* What port based discoveries work?

### Service Enumeration

* Check ALL ports
* Get the OS
  * Does the OS indicate what type of host this might be?
* Note all version numbers
* Are any services running on non-standard ports?

### Web scanning - mapping the surface area

* Auto scanning tools
  * Nikto
  * ZAP
* Identify  the underlying technology
  * Check for standard management directories / interfaces
* Directory enumeration
  * Dirb
  * Gobuster
  * Be sure to go recursive to enumerate as much as possible
* Pay particular attention to any pages with input / file uploads etc.

## Identifying vulnerabilities

* Search exploit-db etc for service versions
* Automatically and manually go through any web apps
* Search Metasploit

## Exploiting vulnerabilities

* RCE is ideal, but any extra information may help with progress elsewhere
* Always be mindful of opportunities to chain exploits
* May also be able to use info from one area / host to exploit another.

## Post exploitation

* Can we achieve privilege exploitation
* See if any new networks or hosts are now available
* Loop through the steps until confident all vulnerabilities have been exploited or confirmed safe or time is up.

</a>

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

## Code execution on SQL server

After gaining access to a SQL instance it is possible to execute commands using `xp_cmdshell`. This command is often disabled, but if we have appropriate privileges then it can be enabled by following the sequence below.

-- this turns on advanced options and is needed to configure xp_cmdshell
`EXEC sp_configure 'show advanced options', '1'`
`RECONFIGURE`
-- this enables xp_cmdshell
`EXEC sp_configure 'xp_cmdshell', '1' `
`RECONFIGURE`

`xp_cmdshell` can then be used to execute code, for example to use powershell to fetch a script onto the target.

<a name='web'>

# Web servers

</a>

<a name='#dir'>

## Directory mapping

</a>

### Gobuster

gobuster can be used in directory enumeration mode to scan for possible directories. This doesn't work recursively.

`gobuster dir -u {target} -w {path to wordlist} -x {extensions to check for, e.g. js,html,php,txt} -o {filename to output}`

### Dirbuster

Dirbuster is an alternative to gobuster that can be set to automatically scan recursively.

Dirbuster comes with a GUI for configuring scan parameters.

<a name='#burp'>

## Burpsuite basics

</a>

<a name='#fuzz'>

## Fuzzing

</a>

### Ffuf

Can use ffuf to fuzz parameters or directories

### Hydra

Hydra can be used to brute force over a number of protocols including ssh, http[s]-{post|get}-form and others.

Example - Brute forcing a http post form login:
`hydra -P sorted.dic -l "Elliot" 10.10.3.188 http-post-form "/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log+In&redirect_to=http%3A%2F%2F10.10.3.188%2Fwp-admin%2F&testcookie=1:S=Location"`

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


## Cracking passwords

If we can capture a handshake on a network, then it is possible to use tools such as Hashcat to launch a dictionary or brute force attack to determine the password for the network.

Hashcat is a good tool for this as it is optimized for usage on a GPU and can give faster times to crack passwords.

<a name='hashes'>

# Hash cracking

</a>

## Hash identification

Before attempting to crack a hash, it is important to identify the type of hash. While tools such as John the Ripper can attempt to identify the hash type being given, there may well be multiple candidates and it is far more efficient if we can tell John or Hashcat the type of hasing algorithm that we're cracking.

There are some useful online tools for hash identification, such as:

[Hash type identifier](https://hashes.com/en/tools/hash_identifier)

## John the ripper

## Hashcat