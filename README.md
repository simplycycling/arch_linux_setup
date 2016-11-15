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
the base install and Gnome set up. Eventually, I'll get as much of this
as possible into Ansible; when I do, I'll post the playbook and roles. Don't
be lazy, though - always do it the hard way, the first time!

## Basic setup for my Arch Linux install

### Install bash completions

Make your life easier from the start - do this ASAP. I do it during the
base install.

    sudo pacman -S bash-completion

### Install xf86-input-synaptics

This package controls the trackpad, and how it reacts to various inputs.
I don't have anything special or fancy set up; at this point, it's just
straight out of the box. Without this package installed, the cursor will
jump to wherever you set your finger on the trackpad. You can still move
it around and click it, but it's super annoying, and reduces functionality
by a considerable margin.

    sudo pacman -S xf86-input-synaptics
### Install OpenSSH

You're probably going to need this.

    sudo pacman -S openssh

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
    
add the udev rules in this repo (or in the Yubico repo - they're the same) 
to /etc/udev/rules.d/ and reboot.

### Set up gpg

This assumes that you are importing pre-existing gpg keys on a Yubikey,
or some other hardware key that works similarly. The previous section
has to be done properly, or this will not work.

First, create new stubs that will point at your Yubikey:

    gpg2 --card-status
    
If this fails, review the "Set up Yubikey" section. 

Next, import your public key. Assuming it's named publickey.txt:

    gpg2 --import < publickey.txt
    
Finally, mark the trust level of your key as "Ultimate"

    gpg2 --key-edit <your identity>
    gpg> trust (it will ask you to confirm)
    gpg> quit

### Set up ssh 

You can read the [Arch Linux wiki page](https://wiki.archlinux.org/index.php/GnuPG#gpg-agent)
for setting up gpg-agent, to get all of the information I'm going to list
here. If you're as lazy as I am, this should work for you.

Create your gpg-agent conf file, if it doesn't already exist:

    touch ~/.gnupg/gpg-agent.conf
    
Open it in vim (I'm just assuming, here), and populate it thusly:

    default-cache-ttl 3600
    pinentry-program /usr/bin/pinentry-gtk-2
    enable-ssh-support

Then add the following to the end of your .bashrc or .bash_profile,
whichever you use for this type of configuration:

    unset SSH_AGENT PID
    if [ "${gnupg_SSH_AUTH_SOCK_by:-0}" -ne $$ ]; then
            export SSH_AUTH_SOCK="/run/user/$UID/gnupg/S.gpg-agent.ssh"
    fi

To see if it worked, open up a new terminal or source your newly modified
bash conf file, and run the following:

    ssh-add -L
    
It should output a long-ish string that ends in a card number - mine looks
like this:
 
    $ ssh-add -L
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC8O1m6tqUYRSINoPYfK9UnGmbokHsSKNGZS+k9ggoanBHhUOR+x/q1aTn12aD6OJU+aU7EbDreGL1/T0fYn8NItxepKaBdjzBisROn+52UONV04h5UjmaWX6hW/MkypmR81Vci4A31HfpDQN2gVgC8A+5sAo92/pkbswZmPzbHNn6Dloy/kciYviEPHky6ilVZbYcN9ppDyrIDDuuKuuKl0wadfpqZK6hilRfXS7seUllA5n/5BxSRG6xruW5XzzLaPTPlACGnvkV8Sf5UYTnlz9rqRM+2LcM4DBRfiDdgtVLZeIlrDFbbODOeZU8XYsaffz2K0Mt+F/B1GhaUBeG5 cardno:000604953496

 
 If it didn't
work, I may have missed something - drop me a line on [twitter](https://twitter.com/rogersherman),
 and we'll suss it out.

### Set up git to sign commits by default

Exactly as it says, this will set up git to sign commits by default. I
love nerdy security shit. GnuPG is for me, as a labcoat is to a scientist - it
 makes them feel "science-y". 
 
    git config --global gpg.program gpg2
    git config --global user.signingkey <your identity here>
    git config --global commit.gpgsign true

### Install Chrome

You'll have to log into your Google account the first time you fire this
up, but you're probably prepared to do that. I'm not judging.

    yaourt google-chrome

You could also do this with packer, if you have it installed. If not,
yaourt is a wrapper for packer and pacman, which I mainly use for
installing packages from AUR, the Arch Users Repository. See the introduction
for how to install yaourt.

### Change screen resolution via xrandr
 
Again, the [Arch Wiki](https://wiki.archlinux.org/index.php/HiDPI) is where
this info comes from, so it's probably worth taking some time to absorb
some of the information there. I am, in fact, basically cutting and pasting
from that, for the rest of this section.

First scale Gnome up to the minimum size which is too big. Usually "2" 
is already too big, but if "2" is still small for you, try "3", etc.

    gsettings set org.gnome.desktop.interface scaling-factor 2
    
Now start scaling down by setting zoom-out factor with xrandr. First get 
the output name:

    xrandr | grep -w connected | cut -d' ' -f1
    
(Rog note - this gave me an output of DP-2, which I will use in the
following example)

Use this value to specify --output further on. If you have two or more 
screens you can set their scale independently. Now, to zoom-out 1.2 
times, run the following:

    xrandr --output DP-2 --scale 1.2x1.2

If the UI is still too big, increase the scale:

    xrandr --output DP-2 --scale 1.25x1.25

The above gave me exactly what I needed on my laptop (late 2013 Macbook 
Pro Retina, 11,3). If you need this for external monitors, it looks like
 it's not much more complex than what I've done here - go read the wiki!
  If you have mouse or trackpad issues - read the wiki.

Just read the wiki. Collective knowledge is almost always smarter than
any one of us.

### Install slack

    yaourt slack desktop

And the peace and quiet that you've been enjoying dies a plaid death.

### Install dotfiles

I've created a [git repo](https://github.com/simplycycling/arch_linux_setup_dotfiles) 
for my dotfiles, which, as I said, I ripped off from Frazelle, who in 
turn ripped them off from somebody else. Feel free to use them for 
inspiration, or mock them as you will.

### Reduce window title bar size

This is another matter of personal taste, but I'm pretty sure that the
guy who came up with the default gnome window file size is the same guy
that decided the default wallpaper.

Copy the gtk.css included in this repo to the correct directory

    cp gtk.css ~/.config/gtk-3.0/
    
That should be all you need.

Install lm_sensors

Install and configure mutt

Install mbpfan (https://github.com/dgraziotin/mbpfan)

Install Go

Install PyCharm

