#shell

##常用命令

    - 统计请求次数最多的连接
        tail -n 10000 hft-touchweb-2018-06-26.log  | 
        grep request |              //过滤
        grep 'xszs/1.0' |           //过滤
        awk -F ':' '{print $7}' |   //匹配
        awk -F ',' '{print $1}' |   //匹配
        sort |                      //排序
        uniq -c |                   //统计
        sort -k1 -rn                //按第一列倒序排
    
    - 查看某进程所拥有的线程
        ps -T -p <pid>
        或
        top -H -p <pid>