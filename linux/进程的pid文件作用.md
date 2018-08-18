#进程的pid文件作用

[https://blog.csdn.net/changli_90/article/details/8911191]

    1) pid文件为文本文件，记录了该进程的pid;
    2) 防止进程启动多个副本，只有获得pid文件（固定路径固定文件名）的写入权限（F_WRLCK）的进程
       才能正常启动，并把自身的pid写入该文件中，否则进程就自动退出；
       ```c
       lock.l_type = F_WRLCK;
       lock.l_whence = SEEK_SET;
       if(fcntl(fd, F_SETLK, &lock) < 0 ) {
            //锁定不成功，退出。。。
       }
       sprintf(buf, "%d\n", (int)pid);
       pidsize = strlen(buf);
       if( (tmp = write(fd, buf, pidsize)) != (int)pidsize ) {
            //写入不成功，退出。。。 
       }
       ```
    3) 如果进程退出，则该进程加的锁自动失效；
    4) 如果进程关闭了该文件描述符fd, 则加的锁失效（整个进程运行期间不能关闭）；
    5) 锁的状态不会被子进程继承；如果进程关闭则锁失效，而不管子进程是否在运行；
    6) 锁的状态(l_type), 锁的位置
        ```
        l_type:
            F_RDLCK 读取锁（分享锁）；
            F_WRLCK 写入锁（排斥锁）；
            F_UNLCK 解锁；
        l_whence:
            SEEK_SET 以文件开始为锁定的起始位置;
            SEEK_CUR 以现在文件读写位置为锁定的起始位置;
            SEEK_END 以文件尾为锁定的起始位置
        ```