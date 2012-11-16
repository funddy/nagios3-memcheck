# nagios3-memcheck #

Nagios3 plugin for monitoring free memory

## Disclaimer ##
This is a plugin I wrote when I was searching for a module with this functionality, but none of the ones I found were clear on how to be used (at least from my perspective). I think it's a good example on how to write a plugin for Nagios, so you can take it as an example or use it at your own risk. It works for me, but it doesn't necessarily mean that it will work for you.

If you think it's not neat to use `free` instead of `/proc/meminfo`, or the way arguments are parsed is not the best, please feel free to improve the plugin :)

## Installation ##
To install this plugin just place it in the nagios plugin directory path. In Debian this directory is `/usr/lib/nagios/plugins` and give it execution permissions as follows:

`chmod +x /usr/lib/nagios/plugins/mem_check`

## Usage ##
The plugin can be used locally or remotely using SSH or nrpe. It takes two mandatory arguments: 

* _-w_: Sets the warning threshold (in percentage) for minimum free memory
* _-c_: Sets the critical threshold (in percentage) for minimum free memory

There's another argument that prints the help: _-h_


### Local use ###
Just add [the config file](https://github.com/amartinj/nagios3-memcheck/blob/master/config/mem.cfg) in one of the directories read by Nagios by default such as `/etc/nagios-plugins/config` or `/etc/nagios3/conf.d/` or any other specified with the `cfg_dir`. You can also add a specific entry location for the file itself with `cfg_file`.

Done this, you can create a service using this command, and associate it to a host or a host group.

### Remote use ###
I recommend you to use nrpe. SSH, when your monitoring tasks grow, can turn into a problem due to the handshake computational cost. I followed [this toturial](http://www.thegeekstuff.com/2008/06/how-to-monitor-remote-linux-host-using-nagios-30/) for understanding the basics of nrpe.

You can configure nrpe to accept arguments for the monitorization tasks, but I wouldn't do that since it's a security risk. What I recommend is to expose the plugin as a command without arguments. This can be easily done adding a line to `/etc/nagios/nrpe.conf` (yes, Debian!) like the following:

`command[check_mem]=/usr/lib/nagios/plugins/check_mem -w 10 -c 0`

It's recommendable to place the plugin somewhere that is familiar to nagios, but that's up to you as long as it's executable.

Now, you just have to query the nrpe server asking it to execute the recently created command:

`/usr/lib/nagios/plugins/check_nrpe -H 10.0.0.253 -c check_mem`

You should get something like this:

`MEM OK - 31% free (158 MB out of 496 MB) |mem=158MB;10;0;0;496`


