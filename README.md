# xibo_vagrant
Vagrantfile and supporting materials for standing up an [Xibo](https://github.com/xibosignage/xibo-cms) CMS server from scratch

I've been playing around with creating a [Vagrantfile](https://www.vagrantup.com/) that can stand up a VM with a running and ready-to-go Xibo CMS server install from scratch. I managed to get through all of the prerequisite installs, configuring the database, and downloading and installing Xibo (1.7.1). What I wind up with is an install that redirects to index.php, ready for the user to walk through the configuration steps.

What I want next is to automate those configuration steps so that I wind up with a ready-to-go (but empty) Xibo server at which I can point clients and get the default layout shown (ideally, without ever touching the server). This seems plausible because all of the configuration steps are entering data that I already know or have the opportunity to make up on-the-spot.

Initial experiments make me think most of the trick is replacing install.php with setup.php...but I suspect that there will be a few additional gotchas that crop up along the way (like encrypting passwords? ...or that "SECRET_KEY" that appears in settings.php?).

Longer-term, it might be nice to even pre-populate the CMS with a few bits of media, a layout, a schedule, etc. And maybe after I get the feel for this, I'll find it fits better as a puppet or chef implementation. But, for now, it's just an inline shell script in the Vagrantfile.
