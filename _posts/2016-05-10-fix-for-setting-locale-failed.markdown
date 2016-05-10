---
layout: post
title: Fix for 'SETTING LOCALE FAILED'
date: '2016-05-10 14:41'
---

I was recently experience some annoying thing. If you choose to install `dokku`
on `DigitalOcean` you will probably get something like so:

```bash
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_CTYPE = "UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to the standard locale ("C").
```

It catching eye and drives me nuts. Following instruction can save you some time
if you have the same.

You just need update your `locale` file  

```bash
$ sudo echo  "LANGUAGE=en_US.UTF-8 \nLC_ALL=en_US.UTF-8 \nLANG=en_US.UTF-8 \nLC_TYPE=en_US.UTF-8" >> /etc/default/locale
```

and reconfigure it

```bash
$ sudo locale-gen en_US.UTF-8
$ sudo dpkg-reconfigure locales
```

As result you gonna see something like this

```bash
dpkg-reconfigure locales
Generating locales...
  en_AG.UTF-8... done
  en_AU.UTF-8... done
  en_BW.UTF-8... done
  en_CA.UTF-8... done
  en_DK.UTF-8... done
  en_GB.UTF-8... done
  ....
```

Don't forget to reconnect, this changes will have effect with new session.
