---
layout: post
title: Logging file system changes in Linux
date: '2015-07-02T10:37:00.002-07:00'
author: John Andersen
tags:
modified_time: '2015-07-02T10:59:10.399-07:00'
blogger_id: tag:blogger.com,1999:blog-8439713708369185140.post-3021848231084889562
blogger_orig_url: http://jsapdx.blogspot.com/2015/07/logging-file-system-changes-in-linux.html
---

First create the logger program

{% highlight c %}
cat > fanlogger.c
#include <fcntl.h>
#include <limits.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/fanotify.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
#define CHK(expr, errcode) if((expr)==errcode) perror(#expr), exit(EXIT_FAILURE)

FILE * log_file;

// Define the function to be called when ctrl-c (SIGINT) signal is sent to process
void signal_callback_handler(int signum)
{
   // Cleanup and close up stuff here
   fclose(log_file);
   // Terminate program
   exit(EXIT_SUCCESS);
}

int main(int argc, char** argv) {
  int fan;
  char buf[4096];
  char fdpath[32];
  char path[PATH_MAX + 1];
  ssize_t buflen, linklen;
  struct fanotify_event_metadata *metadata;
  log_file = fopen(argv[1], "w");
  if (!log_file)
  {
      printf("Error opening file!\n");
      exit(1);
  }
   // Register signal and signal handler
   signal(SIGTERM, signal_callback_handler);
  CHK(fan = fanotify_init(FAN_CLASS_NOTIF, O_RDONLY), -1);
  CHK(fanotify_mark(fan, FAN_MARK_ADD | FAN_MARK_MOUNT,
                    FAN_OPEN | FAN_EVENT_ON_CHILD, AT_FDCWD, "/"), -1);
  for (;;) {
    CHK(buflen = read(fan, buf, sizeof(buf)), -1);
    metadata = (struct fanotify_event_metadata*)&buf;
    while(FAN_EVENT_OK(metadata, buflen)) {
      if (metadata->mask & FAN_Q_OVERFLOW) {
        fprintf(log_file, "Queue overflow!\n");
        continue;
      }
      sprintf(fdpath, "/proc/self/fd/%d", metadata->fd);
      CHK(linklen = readlink(fdpath, path, sizeof(path) - 1), -1);
      path[linklen] = '\0';
      // fprintf(log_file, "%s opened by process %d.\n", path, (int)metadata->pid);
      fprintf(log_file, "%s\n", path);
      close(metadata->fd);
      metadata = FAN_EVENT_NEXT(metadata, buflen);
    }
  }
}
{% endhighlight %}


Then watch as it logs

{% highlight bash %}
root@Graphics:/# gcc fanlogger.c -o fanlogger
root@Graphics:/# ./fanlogger fanlogger.log &amp;
[1] 29
root@Graphics:/# touch file
root@Graphics:/# rm file
root@Graphics:/# kill 29
root@Graphics:/# cat fanlogger.log
/bin/rm
/lib/x86_64-linux-gnu/ld-2.19.so
/etc/ld.so.cache
/lib/x86_64-linux-gnu/libc-2.19.so
/bin/touch
/lib/x86_64-linux-gnu/ld-2.19.so
/etc/ld.so.cache
/lib/x86_64-linux-gnu/libc-2.19.so
/file
{% endhighlight %}
