---
layout: default
---

# Use Secure Shell private/public keys

## Summary

As an HPC Cloud user, you have full control of your virtual machines (VM). This means that you are the system administrator of your own VMs, in other words, the `root` user.

Often authentication to a machine is done with a username/password, but passwords are easy to forget and often not strong enough to withstand cracking. Root access needs good security because it gives full control over the host and is a well known username.

Secure Shell (SSH) offers [public key](https://en.wikipedia.org/wiki/Public-key_cryptography) authentication, a method to access a remote machine securely and with not much trouble.
This is great for allowing passwordless access to a remote system, and also more secure than traditional passwords.  You can find abundant information on the Internet about how it all works.

You need to have a file on your local computer (say, your laptop) with a private key, and you need to install its matching public key on the remote computer(s) you want to access (say, your VM). Then, when you are going to connect to the remote machine from your laptop, the private and public keys will be compared and, if they successfully relate to one-another, your SSH connection will be established.

The mechanism to allow SSH private/public key pairs authentication is already configured (and recommended) on the appliances endorsed by SURFsara and readily available in the _Apps_. Therefore, you need to have a private/public key pair on your laptop in order to be able to connect to a VM created from SURFsara _Apps_.

>**NOTE:**
>
> The instructions described cater for the needs of Linux, Mac and Windows users.

## Preparation

To apply the following instructions you need a terminal on your local machine (laptop).

* Linux and Mac users have a terminal installed by default.
* Windows users have different options. We can recommend [git for windows](https://git-for-windows.github.io/) or [MobaXterm](http://mobaxterm.mobatek.net/).
  * Windows users who prefer PuttyGen should look at the instructions for [Putty tools](putty-tools#generate-ssh-key-on-windows-with-puttygen).

## Generate an SSH private/public key pair

### Check existing SSH keys

The first step is to check if you already have an SSH key. Start a terminal (in Mac/Linux) or GitBash (in Windows). The default location is `~/.ssh/id_rsa` for the private key and  `~/.ssh/id_rsa.pub` for your public key.

Type the following on your terminal:

```bash
ls -l  ~/.ssh/
```

If you see the following files in your output, you already have a key available and can skip to section [Add the key to the local ssh-agent](#add-the-key-to-the-local-ssh-agent).

```
total 72
-rw-------+ 1 user  staff   1679 Feb  1 2017 id_rsa
-rw-r--r--+ 1 user  staff    409 Feb  1 2017 id_rsa.pub
```

### Private key passphrase

When you create a private/public key pair, you will need a passphrase to **protect your local private key**. It is never sent to the remote host.

What is a good passphrase? Choose a long sentence, for example a quote that you like, of more than 35 characters. Because of the length, there is no need to substitute letters with symbols or leave out spaces or punctuation.

But, you say: __I don't want to enter a long passphrase every time I use the key!__
Neither do I, this has been taken care of. You type the passphrase once after logging on to your laptop and a local robot (ssh-agent) will remember your passphrase for the rest of your session.

For more information, please see [Working with SSH key passphrases](https://help.github.com/articles/working-with-ssh-key-passphrases/)

### Generate a private/public key pair

Open a terminal and type `ssh-keygen`. An example dialogue is shown below. Some remarks:

1. Leave the output file name blank for the default file name, or type a variation of `~/.ssh/my_chosen_name`.
2. Do choose and remember an easy but long passphrase.
3. If you forget the passphrase, you need to generate a new key pair and replace the old public keys you installed on remote hosts.

Type the following on your terminal:

```bash
ssh-keygen
```

While interacting with `ssh-keygen` in the terminal, you will see something similar to:

```
Generating public/private rsa key pair.
Enter file in which to save the key (~/.ssh/id_rsa): ### see note 1
Enter passphrase (empty for no passphrase): ### see note 2
Enter same passphrase again:
Your identification has been saved in ~/.ssh/id_rsa
Your public key has been saved in ~/.ssh/id_rsa.pub
The key fingerprint is:
40:1f:33:78:32:51:b5:c4:51:56:99:b6:6a:3d:18:8b user@computer.surfsara.nl
The key s randomart image is:
+---[RSA 2048]----+
|                 |
|               ..|
|      .       .o.|
|       + +.o  o+ |
|      + S.B.o.+.o|
|       E =++oooo+|
|      . =oo= ++.=|
|       *....Bo = |
|      o.o..+o..  |
+----[SHA256]-----+
```

>**NOTE 1:**
>
> You can have many SSH keys, each in a separate file. 
> (Actually, in file pairs: `name` and `name.pub`).

>**NOTE 2:**
>
> Please use a long passphrase to protect your private key.
> The ssh-agent (see below) will assist you so that only need to type the password once per session.


## Using `ssh-agent`

SSH-agent is a service on your computer to remember your ssh passphrase during your local session (that is, until you log out). This way, you do not have to type in that loooong passphrase every time you need to unlock your private key.

### Add the key to the local ssh-agent

Type the following on your terminal:

```bash
ssh-add ~/.ssh/id_rsa ### or the file name you provided to ssh-keygen
```

While interacting with `ssh-add` in the terminal, you will see something similar to:

```
Enter passphrase for ~/.ssh/id_rsa: ### type it in
Identity added: ~/.ssh/id_rsa
```

If this fails because "Could not open a connection to your authentication agent", you need to start the ssh-agent daemon before you run `ssh-add`:

```bash
eval `ssh-agent -s`
```

### List keys currently in ssh-agent

To list the keys loaded in the `ssh-agent` type the following on your terminal:

```bash
ssh-add -l
```

The output will be one line for each key stored in the ssh-agent, similar to:

```
2048 SHA256:ajAxT3T3ZKl2rALBGGmMqufU0n6XAU15lj+fObZEvrI ~/.ssh/id_rsa (RSA)
```

## <a name="copy-your-public-ssh-key"></a> Copy your Public SSH key

You can copy / paste your public key after displaying with the `cat` command:

```bash
cat ~/.ssh/id_rsa.pub
```

On MacOS you can copy it directly into the paste buffer with:

```bash
pbcopy < ~/.ssh/id_rsa.pub
```

After copying the key, you can paste it into your account on OpenNebula or into a user account of your VM.
