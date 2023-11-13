---
author: Tex Nevada
title: "1Password Extension fix for other Browsers on Linux"
date: 2023-06-01T10:00:00+02:00
description: 1Password is a fantastic Password manager. However it doesn't support Vivaldi or other lesser used Web Browsers out of the box. Here is how to fix it.
draft: false
pinned: false
image: # GET AN IMAGE HERE
tags:
- 1Password
- 1Password chrome extension
- 1Password extension
- extension
- Vivaldi
categories:
- 1Password
- Vivaldi
series:
---

# First off

Let's cut to the chase.

* Ensure that **1Password for Linux** is installed along with 1Password in your browser, and that browser integration is enabled from **Settings → Browser** within 1Password for Linux.

Once you have done that. We can check out the file named "custom_allowed_browers" in the resource folder of 1Password. 

`cat /opt/1Password/resources/custom_allowed_browsers`

This should return something similar to this: 
```bash
# This file, when placed into /etc/1password/custom_allowed_browsers will allow for
# custom browsers to be defined that can work with 1Password for Linux's browser extension
# integration.
#
# 1Password for Linux custom browser allowlist
#
# To add a browser here, add the filename of the browser. Multiple can be seperated by a `\n`.
# Any lines starting with `#` will be ignored.
#
# Example:
#
# vivaldi-bin
# opera
```

As we can see. 1Password (while not officially) has somewhat experimental support to enable the extension on other browsers. Examples here listed being Vivaldi and Opera.

This file is a whitelist for 1Password to allow a connection between applications. I would assume 1Password does this for security purposes? Anyway, I will have a complete install script below.

# How to enable it

1. Close 1Password completely.
2. Open a terminal, and run `sudo mkdir /etc/1password`.
3. cd into the folder `cd /etc/1password`.
4. Run `sudo vi custom_allowed_browsers` or the editor of your choice (I just like vim).
5. Paste in the binary name of your web browser executable - such as `opera` or `vivaldi-bin`.
6. Save the file.
7. In the terminal, run `sudo chown root:root /etc/1password/custom_allowed_browsers && sudo chmod 755 /etc/1password/custom_allowed_browsers`
8. Run 1Password - It will now read the new config file and make the appropriate connections.
9. Launch your browser! 😎

### A script for your convenience!

```bash
# Here is an easy script to do it for you =)
sudo mkdir /etc/1password
sudo touch /etc/1password/custom_allowed_browsers
# Just replace Vivaldi with whatever browser you want to use.
echo "vivaldi-bin" > /etc/1password/custom_allowed_browsers
sudo chown root:root /etc/1password/custom_allowed_browsers
sudo chmod 755 /etc/1password/custom_allowed_browsers
```

That's it!