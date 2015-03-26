# check_primo_pipes
A plugin for Nagios (and other compatible monitoring services) to monitor the status of pipes in Ex Libris Primo library discovery system. It will alert in case of pipes in state: stopped error, stopped harvest error, and threshold exceeded error.

## About
As Ex Libris Primo does not provide users with a back office API this plugin can monitor the back office pipe status screen using "screen scraping" via the HTML.

Currently the plugin cannot identify stale pipes but this can be implemented.

## Installation

Copy the check_primo and primo_searches.txt files to your Nagios plugins directory, e.g.:

    git clone https://github.com/aalborgunilib/check_primo_pipes
    cd check_primo_pipes
    sudo cp plugins/check_primo_pipes /usr/lib64/nagios/plugins/
    sudo chmod 755 /usr/lib64/nagios/plugins/check_primo_pipes

Install Perl dependencies for the plugin via e.g. [cpanm](https://metacpan.org/pod/App::cpanminus). (Please stay within the check_primo_pipes directory):

    cpanm --sudo --installdeps .

Now, check to see if the plugin is working:

    plugins/check_primo --critical=6 --hostname=http://<server>.hosted.exlibrisgroup.com --port=1701 --institution=INST --local

You should get something in the line of:

    PRIMO_PIPES CRITICAL - status 'completed': 14, status 'stopped harvest error': 1, status 'terminated': 1

or    

    PRIMO_PIPES OK - status 'completed': 14, status 'terminated': 2

## Usage

    Usage: check_primo_pipes
        [ -H|--hostname=<Primo back office URL> ]
        [ -i|--customerid=<customer id> ]
        [ -u|--username=<user name> ]
        [ -p|--password=<password> ]
        [ -n|--pipe=<pipe name>]
        [ -j|--cookiejar=<filename> ]
        [ -t|--timeout=<timeout> ]
        [ -v|--verbose ]

`-H|--hostname` is the url to the Primo server (including http:// or https://). Remember to include the port number (Ex Libris usually defaults to 1701).

`-i|--customerid` is a unique number that needs to be extracted from the HTML source code of the login screen to the Primo back office. Look for `<input type="hidden" value="1234567890" name="customerId" />` and take the number from the value attribute.

`-u|--username` and `-p|--password` are your login credentials to the back office. I recommend that you create a user specifically for the monitoring.

`-t|--timeout` is the plugin timeout. If timeout is reached, the check will bail out and issue an UNKNOWN state.

### Icinga 2 configuration ###

## Todo

* Identify stale pipes

## Copyright and license

Copyright (c) 2015 Kasper Løvschall and Aalborg University Library

This software is free software; you can redistribute it and/or modify it under the same terms as Perl itself. See [Perl Licensing](http://dev.perl.org/licenses/).
