# check_primo_pipes
A plugin for Nagios (and other compatible monitoring services) to monitor the status of pipes in Ex Libris Primo library discovery system. It will alert in case of pipes in state: stopped error, stopped harvest error, and threshold exceeded error.

It also features rudimentary support for identifying stalled pipes. This works if you provide an expected maximum (in hours) a pipe should be in a running state. If a pipe exceeds the threshold, a warning is issued.

## About
As Ex Libris Primo does not provide users with an API to the back office this plugin can monitor pipes using "screen scraping" via the HTML.

The plugin can monitor all available pipes in one go or a just a single pipe at a time. It supports a compact status output or a more detailed list of the current state of the different pipes.

The plugin will require user credentials for the Primo back office and it is recommend that you create a user specifically for monitoring purposes. A user with the role "Staff User" is needed. in addition, the server performing the monitoring will need to have access to the back office URL through the firewall of the Primo server.

## Installation

Copy the check_primo_pipes file to your Nagios plugins directory, e.g.:

    git clone https://github.com/aalborgunilib/check_primo_pipes
    cd check_primo_pipes
    sudo cp plugins/check_primo_pipes /usr/lib64/nagios/plugins/
    sudo chmod 755 /usr/lib64/nagios/plugins/check_primo_pipes

Install Perl dependencies for the plugin via e.g. [cpanm](https://metacpan.org/pod/App::cpanminus). (Please stay within the check_primo_pipes directory):

    cpanm --sudo --installdeps .

Now, check to see if the plugin is working:

    plugins/check_primo_pipes -H <http://server.hosted.exlibrisgroup.com:1701> -i <customerid> -u <username> -p <password>

You should get something in the line of:

    PRIMO_PIPES OK - status 'completed': 14, status 'terminated': 2
    
Or maybe (if something is wrong):

    PRIMO_PIPES CRITICAL - status 'completed': 14, status 'stopped harvest error': 1, status 'terminated': 1

## Usage

    Usage: check_primo_pipes
        [ -H|--hostname=<Primo back office URL> ]
        [ -i|--customerid=<customer id> ]
        [ -u|--username=<user name> ]
        [ -p|--password=<password> ]
        [ -n|--pipe=<pipe name>]
        [ -T|--hours=<max number of hours> ]
        [ -j|--cookiejar=<filename> ]
        [ -t|--timeout=<timeout> ]
        [ -v|--verbose ]

`-H|--hostname` is the url to the Primo server (including http:// or https://). Remember to include the port number (Ex Libris usually defaults to 1701).

`-i|--customerid` is a unique number that needs to be extracted from the HTML source code of the login screen to the Primo back office. Look for `<input type="hidden" value="1234567890" name="customerId" />` and take the number from the value attribute.

`-u|--username` and `-p|--password` are your login credentials to the back office.

`-n|--pipe` is the name of a single pipe that will be checked. If omitted all pipes will be checked.

`-T|--hours` is the maximum number of hours any pipe should complete in. If the threshold is exceeded a warning is issued. If omitted detection for stalled pipes is disabled.

`-j|--cookiejar` is the filename (including path) where session cookies will be stored. It defaults to `/tmp/check_primo_pipes_cookiejar.dat`.

`-t|--timeout` is the plugin timeout. If timeout is reached, the check will bail out and issue an UNKNOWN state.

`-v|--verbose` will switch to the more detailed status view.

## Todo or future ideas

* Make stalled pipe detection more sophisticated
* Make it possible to define a list of pipes to include / exclude
* Monitor the process list
* Monitor the execution time of pipes (performance data)
* Monitor errors in a pipe run

## Copyright and license

Copyright (c) 2015 Kasper Løvschall and Aalborg University Library

This software is free software; you can redistribute it and/or modify it under the same terms as Perl itself. See [Perl Licensing](http://dev.perl.org/licenses/).
