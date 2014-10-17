## Overview

There is a great project called Bash Paranoia. Right now their site is busted so I can’t link to it. Its a patch that applies to bash that allows commands to be logged to syslog. I basically took this one step further and added curl support.

I have more information about this patch at the following page

http://zcentric.com/programming-work/bash-paranoia-curl-logger/

My original blog posting about this was here

http://zcentric.com/2010/03/09/bash-command-logger-with-curl-support/

## Install

### Apply the patch

If you are looking for a 3.x patch view the bash-3.x branch or at the following URL

https://github.com/mzupan/bash-paranoia-curl/tree/bash-3.x


This should apply to all the 4.x versions for bash. This assumes you have bash v3.2 extracted and want to patch that. So first apply the paranoia patch

    wget https://raw.githubusercontent.com/mzupan/bash-paranoia-curl/master/bash-paranoia.patch
    wget https://raw.githubusercontent.com/mzupan/bash-paranoia-curl/master/bash-paranoia-curl.patch
    cd bash-4.2
    patch -p1 < ../bash-paranoia.patch

Now apply the curl patch. This patch assumes you have 64bit OS installed. You can edit the patch to switch lib64 to lib at the bottom of the patch and it will apply just fine. I am working on a way to add this to configure

    patch -p1 < ../bash-paranoia-curl.patch

### Build bash

Now lets compile bash. 

    ./configure --enable-paranoia #you can include  other configure flags here
    make
    make install

Now lets make the config script. You need to make the following file

    /etc/bash.conf

Now you want your curl collector script endpoint. 

    URL=http://1.1.1.1/endpoint/

### MySQL table

Your MySQL table might look something like this

    CREATE TABLE `commandlog` (
    `id` int(11) NOT NULL auto_increment,
    `server` varchar(100) NOT NULL,
    `user_login` varchar(100) NOT NULL,
    `user_run` varchar(100) NOT NULL,
    `ip` varchar(100) NOT NULL,
    `session` varchar(100) NOT NULL,
    `command` longtext NOT NULL,
    `ts` datetime NOT NULL,
    PRIMARY KEY  (`id`)
    ) ENGINE=MyISAM AUTO_INCREMENT=1 DEFAULT CHARSET=latin1;

### Collector Script

This is just a non-working PHP example of what your script might look like

    <?php
    $server = $_SERVER['REMOTE_ADDR'];
    $user_login = $_POST['user_login'];
    $user_run =  $_POST['user_run'];
    $ip =  $_POST['ip'];
    $session =  $_POST['session'];
    $command =  $_POST['command'];
    $ts = time();
    $sql = “INSERT INTO commandlog(server,user_login,user_run,ip,session,command,ts)         VALUES(‘$server’,'$user_login’,'$user_run’,'$ip’,'$session’,'$command’,'$ts’)”;
    // place into sql now.. too lazy to do this for you
    ?>
