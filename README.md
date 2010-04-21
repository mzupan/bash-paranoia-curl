## Overview

There is a great project called Bash Paranoia. Right now their site is busted so I can’t link to it. Its a patch that applies to bash that allows commands to be logged to syslog. I basically took this one step further and added curl support.

I have more information about this patch at the following page

http://zcentric.com/programming-work/bash-paranoia-curl-logger/

My original blog posting about this was here

http://zcentric.com/2010/03/09/bash-command-logger-with-curl-support/

## Install

### Apply the patch

This should apply to all the 3.x versions for bash. This assumes you have bash v3.2 extracted and want to patch that. So first apply the paranoia patch

    patch -p0 < ../bash-paranoia.patch

Now apply the curl patch. This patch assumes you have 64bit OS installed. You can edit the patch to switch lib64 to lib at the bottom of the patch and it will apply just fine. I am working on a way to add this to configure

    patch -p1 < ../bash-paranoia-curl.patch

### Build bash

Now lets compile bash. 

    ./configure ––enable-paranoia #you can include  other configure flags here
    make
    make install

Now lets make the config script. You need to make the following file

    /etc/bash.conf

Now you want your curl collector script endpoint. 

    URL=http://1.1.1.1/endpoint/