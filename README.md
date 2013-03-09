# httpgetcheck

A simple perl script for monitoring a URL for changes and calling a phone number via Twilio when a change is detected.

httpgetcheck requests a URL and compares the response content against the previous run. If the content has changed, a Twilio call is made to each specified recipient phone number. httpgetcheck also makes a heartbeat call at the specified interval so that recipients can be assured checks are being performed even if content has not changed.

Because you probably don't enjoy being awakened by false alarms, you can specify a start hour and end hour for squelching heartbeat calls. During this period, content changes will still result in a call.

In short, this is the script I use to wake me up when the [WWDC page](https://developer.apple.com/wwdc/ "WWDC") has changed.

You'll need your own Twilio account, because I'm cheap and don't want to pay for your phone calls. You can get one at [http://www.twilio.com/](http://www.twilio.com/ "Twilio Home Page").

The script does not run as a daemon. It is designed to be run via a cron job at the interval of your choosing.

## Usage

httpgetcheck [options]

Options:

    -help   Prints a brief help message and exits.

    -man    Prints the manual page and exits.

    -data-dir=DIR
            Specifies the directory to which content and state is saved. The
            default value is "/var/httpgetcheck". If the directory does not
            exist, it will be created.

    -twilio-account-sid=SID
            Specifies the account sid for the Twilio api account to use.

    -twilio-auth-token=TOKEN
            Specifies the auth token for the Twilio api account to use.

    -twilio-phone-number=PHONE
            Specifies the outgoing phone number in RFC 3966 format to use
            for Twilio calls.

    -heartbeat-interval=INTERVAL
            Specifies the interval (in seconds) between heartbeat calls. The
            default is 7 * 24 * 60 * 60 or 7 "days". Setting the interval to
            0 disables heartbeat calls.

    -heartbeat-squelch-start-hour=HOUR
            Specifies the local hour of the day at which heartbeat calls
            should not be made. Setting the hour to -1, the default,
            disables squelching.

    -heartbeat-squelch-end-hour=HOUR
            Specifies the local hour of the day at which heartbeat calls
            should resume. Setting the hour to -1, the default, disables
            squelching.

    -check-url=URL
            The HTTP(S) URL to get for content checking.

    -content-changed-message=MESSAGE
            The text to say when announcing content changes. The checked URL
            will be appended to the message.

    -heartbeat-message=MESSAGE
            Specifies the text to say during a heartbeat call.

    -recipient=PHONE
            Specifies a phone number in RFC 3966 format to call for alerts.
            Multiple recipents can be spcified using a -recipient option for
            each phone number.

## crontab example

The following is an example crontab entry for Ubuntu Linux. It assumes that httpgetcheck is in the defined PATH. The script will be run every 5 minutes.

    */5 * * * * dhthomas httpgetcheck -twilio-account-sid='yoursid' -twilio-auth-token='yourtoken' -twilio-phone-number='+16505551234' -data-dir=/home/dhthomas/var/wwdccheck -check-url='https://developer.apple.com/wwdc/' -recipient='+14155551212' -heartbeat-squelch-start-hour=22 -heartbeat-squelch-end-hour=9

