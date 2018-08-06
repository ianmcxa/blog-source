---
title: "Locking myself out of my Linux Server (and breaking in again)"
date: 2018-08-05T23:31:41-04:00
---

I have a little Linux VPS server that I use for various things. It runs some of the sites I've made over the years, and runs a Deluge, which if your not familiar, is a torrent client. It's useful for seeding Linux ISOs and sharing files and whatnot.

A few months back I was going to update the server to the next Ubuntu version, when I noticed that my user didn't have sudo rights. I tried to login as root, but I hadn't set a root password. I think I had been using the root account with my ssh key, but somewhere along the line I had disabled root login without giving my other user account sudo access.a

So there I was, locked out of my server. I don't have physical access, and the hosting provider can't do anything short of re-imaging the machine. I did have backups of the sites on the server, but it would be a pain to set everything up again. So I started to size up my options.

I did have a standard user account, with which I could log in and access my home directory. At this point, all I needed was a privilege escalation vulnerability and the sever would be mine again. I thought about attempting to exploit an old Linux CVE, but the system's kernel was relatively recent, so that didn't seem promising. At this point, I remembered a rather stupid mistake I had made when I was originally setting up the sever. I had forgotten to run the Deluge web UI under a normal user account. It was just running as root. The UI was running behind an SSH client cert, but I still had access. So if I could exploit Deluge in any way, I would have my server back.

(As an aside from the story, if you are running any web control or admin dashboard, you should really be using SSH client certificates to protect it. Public/private keys are vastly superior to passwords, and Nginx makes it relatively easy to set them up)

Next, I started to look through the Deluge web UI for anything useful. While digging through the settings, I came across plugins and as luck would have it, there was a Deluge plugin for running a custom script when a torrent was added or removed. From there it was a relatively simple matter of writing a bash script to set the root password, restarting Deluge which I ended up having to restart the entire box to do, and adding a torrent. From there I could su to the root user and the server was mine again.

The moral of this story is...don't lose your root passwords, and make sure your sudo settings are correct. Also, if you're running a web UI without strong security it may be easier than you think to exploit it and gain control of your server.
