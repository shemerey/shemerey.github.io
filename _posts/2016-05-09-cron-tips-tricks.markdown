---
layout: post
title: Cron tips & tricks
date: '2016-05-09 14:28'
---

CronTab is pretty powerful app, but we often don't pay attention to it, here is some tips i've found useful for me.

### Format

```bash
*    *    *    *    *  command to be executed
┬    ┬    ┬    ┬    ┬
│    │    │    │    │
│    │    │    │    │
│    │    │    │    └───── day of week (0 - 6)
│    │    │    └────────── month (1 - 12)
│    │    └─────────────── day of month (1 - 31)
│    └──────────────────── hour (0 - 23)
└───────────────────────── min (0 - 59)
```

### Some Examples

```bash
0 * * * *     every hour
*/15 * * * *  every 15 mins
0 */2 * * *   every 2 hours
0 0 0 * 0     every sunday midnight
```

### Basic commands

 Open in editor
```bash
$ crontab -e
```

List tasks
```bash
crontab -l [-u user]
```

### Cron Tab checkers

It's hard to remember this format, and crontab doesn't provide a handy way to check your syntax, like `nginx` does for instance. But there is plenty of checkers online what you can use.

* [http://crontab.guru/](http://crontab.guru/)
* [http://cron.schlitt.info/](http://cron.schlitt.info/)
* [http://crontab-generator.org/](http://crontab-generator.org/)

### Apply the principle of least privilege

The sixth column of a system crontab(5) file is the username of the user as which the task should run:

```crontab
0 * * * *  root  cron-task
```

To the extent that is practical, you should run the task as a user with only the privileges it needs to run, and nothing else. This can sometimes make it worthwhile to create a dedicated system user purely for running scheduled tasks relevant to your application.

```crontab
0 * * * *  myappcron  cron-task
```

This is not just for security reasons, although those are good ones; Similarly, for tasks with database systems such as `MySQL`, don’t use the administrative root user if you can avoid it; instead, use or even create a dedicated user with a unique random password stored in a locked-down `~/.my.cnf` file, with only the needed permissions. For a `MySQL` backup task, for example, only a few permissions should be required, including `SELECT`, `SHOW VIEW`, and `LOCK TABLES`.

### Customize your tasks properly

If necessary, you can set arbitrary environment variables for the tasks at the top of the file:

```crontab
MYVAR=myvalue

0 * * * *  you  cron-task-which-use-env-vars
```

### Send the output somewhere useful

Another common mistake is failing to set a useful MAILTO at the top of the crontab(5) file, as the specified destination for any output and errors from the tasks. cron(8) uses the system mail implementation to send its messages, and typically, default configurations for mail agents will simply send the message to an mbox file in `/var/mail/$USER`, that they may not ever read. This defeats much of the point of mailing output and errors.

This is easily dealt with, though; ensure that you can send a message to an address you actually do check from the server, perhaps using mail(1):

```bash
$ printf '%s\n' 'Test message' | mail -s 'Test subject' you@example.com
```

Once you’ve verified that your mail agent is correctly configured and that the mail arrives in your inbox, set the address in a `MAILTO` variable at the top of your file:

```crontab
MAILTO=you@example.com

0 * * * *    you  cron-task-1
*/5 * * * *  you  cron-task-2
```

If you don’t want to use email for routine output, another method that works is sending the output to syslog with a tool like logger(1):

```crontab
0 * * * *   you  cron-task | logger -it cron-task
```

Alternatively, you can configure aliases on your system to forward system mail destined for you on to an address you check. For Postfix, you’d use an aliases(5) file.

I sometimes use this setup in cases where the task is expected to emit a few lines of output which might be useful for later review, but send stderr output via `MAILTO` as normal. If you’d rather not use syslog, perhaps because the output is high in volume and/or frequency, you can always set up a log file `/var/log/cron-task.log` … but don’t forget to add a `logrotate` for it!
