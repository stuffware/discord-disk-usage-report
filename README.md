# discord-disk-usage-report

Pretty disk usage reports, posted by cron to a Discord channel via webhook (curl).

When configured as described below, this script will regularly post messages to Discord that look like the following:

![screenshot](https://raw.githubusercontent.com/mturley/curl-discord-cronjob/master/screenshot2.png)

## Dependencies

* bash
* awk
* curl
* python (any old version, just for JSON escaping)
* cron, or something like cron, to make it act like a "service".

## Setup

1. Clone this repo on a server somewhere.
2. Edit the top of the `disk-usage-report` script so the `webhook_url` variable contains your real Discord Webhook URL.
3. Give the script a test run! You can just type `./disk-usage-report` at the command line.
4. If your message showed up in Discord, you're almost done. I have written a quick `setup` script to set up cron for hourly disk usage reports, using `cron.hourly`. You can use it just by running `./setup` from the root of this repo, once, any time (before or after you make changes to the script, because it is a symbolic link, not a file copy).

* **If you used the `setup` script, move on to step 5.** If you want to set it up yourself instead, you will first have to set up the ProgressBar submodule:

   ```sh
   cd ProgressBar
   git submodule init
   git submodule update
   cd ..
   ```

   Then, there are a number of ways you can automate the webhook, but the simplest is to just use `cron`. You have two options for adding a `cron` job:

   * You can configure any custom timing you like with `sudo crontab -e`, which requires you to read the manual a little bit...
   
   * ...or if you just want the webhook to be called hourly, daily, weekly, or monthly (etc), if your system is running a new enough version of `cron`, you can just symbolically link your script into `/etc/cron.daily/` or `/etc/cron.hourly/`, etc, if they are present on your system:

   ```sh
   sudo ln -s /path/to/discord-disk-usage-report/disk-usage-report /etc/cron.hourly/
   ```

   This is what my `setup` script does. This will schedule an hourly disk usage report to be posted to your Discord channel! 🎉

5. You can test it with `sudo su` followed by `run-parts /etc/cron.hourly`, but beware that that will run EVERYTHING in `cron.hourly`. Note also that simply calling it with `sudo run-parts` will not change to the root user like cron does, so it's not a true test (just in case). Alternatively, you can just wait an hour/day for cron to test it for you.

6. Optionally, you can edit the `TZ=` portion of the `disk-usage-report` script to change the timezone of the `timestamp` variable, or edit the area between the two `EOM`s to change the exact message being posted. Have fun, and let me know if you do anything cool with it!

## Next Steps for this repo

I would really like to make this script not repeat itself, if the disk usage hasn't changed in the last hour. So there isn't a bunch of activity in that discord channel when things are not being downloaded / moved around.

## About the `send-message` script

Note: This repo is a fork of my [mturley/curl-discord-cronjob](https://github.com/mturley/curl-discord-cronjob) repo, with the `disk-usage-report` script replacing the `example` script found there. See the README over there for more details on how the `send-message` script works.
