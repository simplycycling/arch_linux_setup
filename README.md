# Arch Linux Setup

## Introduction

This is somewhat inspired by [Jess Frazelle's excellent guide for getting
a proper dev environment set up on a Macbook Pro](https://github.com/jessfraz/mac-dev-setup)
 (which I contributed to! My first ever open source contribution!). 
Since I use an MBP, and was very new to OS X when I first came across 
her guide, it was quite a resource for me. I'm also a big fan of the 
dotfiles she uses (which she in turn snagged from somebody else), and I 
use so much of what she set up that I'd actually be embarrassed if she 
were to see my bash prompt (the way hers is set up just made so much 
sense!). 

So if your intention is to set up Arch Linux, I'm creating this guide to
hopefully make your life a little easier. This guide does assume that
you have your base install and WM/WE all ready to go. If you aren't at
that point yet, you're going to want to start with [another excellent guide](http://zanshin.net/2015/02/05/arch-linux-on-a-macbook-pro-part-1-creating-a-usb-installer/)
to actually set up dual booting, and get the base Arch Linux install going.
There are a couple of typos and mistakes in that article (actually series
of articles), but they're trivial. Also, do note that the packer info is
out of date - my recommendation woud be to [install yaourt](https://www.ostechnix.com/install-yaourt-arch-linux/)
when you get to the point in Mark's articles, where you actually need
packer. IIRC, that's in part 3.

At this point, this is basically just a run list of what I do after I get
the base install and Gnome set up.

## Basic setup for my Arch Linux install

### Install xf86-input-synaptics

This package controls the trackpad, and how it reacts to various inputs.
I don't have anything special or fancy set up; at this point, it's just
straight out of the box. Without this package installed, the cursor will
jump to wherever you set your finger on the trackpad. You can still move
it around and click it, but it's super annoying, and reduces functionality
by a considerable margin.

    sudo pacman -S xf86-input-synaptics

### Change to dark theme 

This is a matter of personal taste, YMMV. But I do like dark themes. Open
Gnome Tweak Tool, select the Appearance tab, and turn the Global Dark
Theme on.

I change the background at this point, as well. Whoever decided the default
Gnome background is probably the same guy who decided that a terminal
that you can't use a transparency on is a great idea. He's also probably
a terrorist. Hates us for our transparencies. 

### Set up yubikey

I use a Yubikey Nano as two factor authentication with Google and Github,
as well as for storing my gpg keys, and ssh authentication.

    pacman -S yubikey-personalization
    add udev rules to /etc/udev/rules.d/
    reboot

### Set up git to sign commits by default



### Install Chrome

You'll have to log into your Google account the first time you fire this
up, but you're probably prepared to do that. I'm not judging.

    yaourt google-chrome

You could also do this with packer, if you have it installed. If not,
yaourt is a wrapper for packer and pacman, which I mainly use for
installing packages from AUR, the Arch Users Repository. See the introduction
for how to install yaourt.

### Change screen resolution via xrandr
 
(https://wiki.archlinux.org/index.php/HiDPI)

Install slack

Set up gpg

Set up ssh (https://wiki.archlinux.org/index.php/GnuPG#gpg-agent)

Install dotfiles

Add .css to change GTK title bar, which is way too goddamn big (https://unix.stackexchange.com/questions/276951/how-to-change-the-titlebar-height-in-standard-gtk-apps-and-those-with-headerbars)

Install lm_sensors

Install and configure mutt

Install mbpfan (https://github.com/dgraziotin/mbpfan)

Install Go

Install PyCharm

