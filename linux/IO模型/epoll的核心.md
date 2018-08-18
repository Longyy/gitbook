#epoll的核心代码



```
while(true)
{
    //等待EPOLL的事件发生，相当于监听，事件要在初始化的时候绑定；
    int nfds = epoll_wait(m_epoll_fd, m_events, MAX_EVENTS, EPOLL_TIME_OUT);
    
    if(nfds <= 0) {
        continue;
    }
    
    m_bOnTimeChecking = false;
    
    G_CurTime = time(NULL);
    
    for(int i=0; i < nfds; i++) {
        
        try {
            
            if(m_events[i].data.fd == m_listen_http_fd) { //如果监听到有http连接到绑定的端口，则建立新的连接；
                OnAcceptHttpEpoll();
            } else if (m_events[i].data.fd == m_listen_sock_fd) { //如果有socket连接到绑定的端口，则建立连接；
                OnAcceptSocketEpoll();
            } else if (m_events[i].events && EPOLLIN) { //如果是已经连接的用户，并且收到数据，那么进行读入
                OnReadEpoll();
            }
            
            OnWriteEpoll(); //查看当前活动连接是否有需要写出的数据；
        
        } catch(int) {
            //..
        }
    }
}

```

##Epoll常用事件类型

    EPOLLIN: 表示对应的文件描述符可以读;
    EPOLLOUT: 表示对应的文件描述符可以写；
    EPOLLPRI: 表示对应的文件描述符有紧急的数据可读;
    EPOLLERR: 表示对应的文件描述符发生错误；
    EPOLLHUP: 表示对应的文件描述符被挂断；
    EPOLLET: 表示对应的文件描述符有事件发生；