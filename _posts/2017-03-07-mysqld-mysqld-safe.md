---
layout: post
title: ' MySQL 启动 mysqld & mysqld_safe'
tags: [IT]
description: >
  
---

# mysqld 和mysqld_safe

` mysqld_safe`  是一个可执行的  shell 脚本，会在启动  mysql 服务器后继续监控其运行情况，并在 mysqld 死机时重新启动它
用来测试当前是否有 mysqld 进程如果不存在就 restart  

```
while true
do
  # Some extra safety
  if [ ! -h "$safe_mysql_unix_port" ]; then
    rm -f "$safe_mysql_unix_port"
  fi
  if [ ! -h "$pid_file" ]; then
    rm -f "$pid_file"
  fi

  start_time=`date +%M%S`

  eval_log_error "$cmd"

  if [ $want_syslog -eq 0 -a ! -f "$err_log" -a ! -h "$err_log" ]; then
    touch "$err_log"                    # hypothetical: log was renamed but not
    chown $user "$err_log"              # flushed yet. we'd recreate it with
    chmod "$fmode" "$err_log"           # wrong owner next time we log, so set
  fi                                    # it up correctly while we can!

  end_time=`date +%M%S`

  if test ! -f "$pid_file"              # This is removed if normal shutdown
  then
    break
  fi


  # sanity check if time reading is sane and there's sleep
  if test $end_time -gt 0 -a $have_sleep -gt 0
  then
    # throttle down the fast restarts
    if test $end_time -eq $start_time
    then
      fast_restart=`expr $fast_restart + 1`
      if test $fast_restart -ge $max_fast_restarts
      then
        log_notice "The server is respawning too fast. Sleeping for 1 second."
        sleep 1
        sleep_state=$?
          have_sleep=0
        fi

        fast_restart=0
      fi
    else
      fast_restart=0
    fi
  fi

  if true && test $KILL_MYSQLD -eq 1
  then
    # Test if one process was hanging.
    # This is only a fix for Linux (running as base 3 mysqld processes)
    # but should work for the rest of the servers.
    # The only thing is ps x => redhat 5 gives warnings when using ps -x.
    # kill -9 is used or the process won't react on the kill.
    numofproces=`ps xaww | grep -v "grep" | grep "$ledir/$MYSQLD\>" | grep -c "pid-file=$pid_file"`

    log_notice "Number of processes running now: $numofproces"
    I=1
    while test "$I" -le "$numofproces"
    do
      PROC=`ps xaww | grep "$ledir/$MYSQLD\>" | grep -v "grep" | grep "pid-file=$pid_file" | sed -n '$p'`

      for T in $PROC
      do
        break
      done
      #    echo "TEST $I - $T **"
      if kill -9 $T
      then
        log_error "$MYSQLD process hanging, pid $T - killed"
      else
        break
      fi
      I=`expr $I + 1`
    done
  fi
  log_notice "mysqld restarted"
done

log_notice "mysqld from pid file $pid_file ended"


```

mysqld 才是二进制文件

```powershell
# 查看文件的类型， 会发现mysqld 是二进制文件  mysqld_safe是shell脚本

root@moon:/usr/local/mysql/bin# file /usr/local/mysql/bin/mysqld
/usr/local/mysql/bin/mysqld: ELF 64-bit LSB executable, x86-64, version 1 (GNU/Linux), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=7c8abfc71b95a74d7804f89e41bdc902e22f4ea9, not stripped

# mysqld_safe是shell脚本
root@moon:/usr/local/mysql/bin# file /usr/local/mysql/bin/mysqld_safe
/usr/local/mysql/bin/mysqld_safe: POSIX shell script, ASCII text executable

```

如果把当前的mysqld  kill掉，会发现mysqld的进程还是存在

```powershell
# 查看当前的mysqld进程

ps -ef |grep mysqld

root@moon:/usr/local/mysql/bin# ps -ef |grep mysqld
root      8137  5813  0 19:41 pts/0    00:00:00 grep --color=auto mysqld
root     16502     1  0 Feb24 ?        00:00:00 /bin/sh /usr/local/mysql/bin/mysqld_safe --datadir=/usr/local/mysql/var --pid-file=/usr/local/mysql/var/moon.pid
mysql    16991 16502  0 Feb24 ?        00:03:23 /usr/local/mysql/bin/mysqld --basedir=/usr/local/mysql --datadir=/usr/local/mysql/var --plugin-dir=/usr/local/mysql/lib/plugin --user=mysql --log-error=/usr/local/mysql/var/moon.err --open-files-limit=65535 --pid-file=/usr/local/mysql/var/moon.pid --socket=/tmp/mysql.sock --port=3306
root@moon:/usr/local/mysql/bin# 


# 杀死mysqld 
kill -9 进程的ID

# 再查看当前的mysqld进程 ， 发现会仍然存在  , 只不过是进程ID 换了   
ps -ef | grep mysqld

# 真正杀死mysql 进程 ，所以不能只kill mysqld ，可以通过killall 命令把 mysqld 和mysqld_safe 同时kill 掉
killall -9 mysqld mysqld_safe

ps -ef | grep mysqld

# 然后用mysqld 启动  ，就没有mysqld_safe
```


