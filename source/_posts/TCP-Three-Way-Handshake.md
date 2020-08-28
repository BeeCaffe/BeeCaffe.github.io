---
title: TCP Three Way Handshake
date: 2020-05-15 22:31:35
tags: network
---
## 1. TCP Header Format
As is shown in the following picture, the TCP header has 20 Bytes in total.

![16.JPG](/images/16.png)

* **Port Number**: Each TCP segment includes source port number and destination port number, which respectively refers to the TCP segment sender's and receiver's address. Notes to mention that, here we utilizes "port" rather than "address", that is said TCP answer for application layer and itself belong to tranport layer. Meanwhile, the combination of "port" and "IP address" is called socket, which is able to ensure a specific program in a computer. 

* **Sequence Number**: Behind source and destination port, 32 bits have been utilized to reserver sequence number, which is randomlly produced during Three-Way Handshake process and used to ensure the correction of the TCP segment order. TCP is an order protocal, and the sequence number is used to make sure TCP message can be sequential sent to another computer's application layer.
* **Acknowledgement Number**: Then, there is 32 bits Ackknowledgement Number in TCP Header. We call it as ack-number for simplify. Ack-numebr is mainly used to response last received TCP message that your message I have received.
* **Header Length*: The length of TCP header, usually is 20 Bytes.
* **Flag Bits**:
    * **URG=1**: enable Uregent Pointer
    * **ACK=1**: enable Acknowledgement Number 
    * **PSH=1**: This message should be submitted to application layer as soon as possible.
    * **RST=1**: reset connection
    * **SYN=1**: open a new connection
    * **FIN=1**: finish a connection
* **CheckSum and Urgent Pinter**:
**CheckSum** is utilized to check correctness of TCP message to avoid the loss of data in transmission process. In TCP protocol, if transport find that the TCP package in error, it will request the Client/Server resend this package. Urgent pointer, I do not know the usage of this segment.

* **Notes**:
    * Acknowledge Number(ack) and ACK flag are different things in TCP header.

## 2. Three-Way Handshake
Three-Way Handshake refers to that the client and server link establish process requires three data package in total.This process is generally raised by client function **connect()**, and the mainly process of three way handshake is shown in the following picture.

![17.JPG](/images/17.png)


* **First-Way Handshake**:
Client sets SYN bits as 1, that is SYN=1, and randomly generates a sequence value i, that is seq=i, and sends data package to server. After that client changes state to SYN-SENT and waits for the ackknowledgement of server. Meanwhile, server keeps LISTEN state until the data package come in.
* **Second-Way Handshake**:
Server receive the first package and check the SYN bit. When SYN=1, server knows that client suppose to establish connection with himself. Then the server responds an data package and sets ACK and SYN bit as 1. acknowledgement sequence assigns to i+1, that is ack=i+1, as a respose for first data package. Meanwhile, server also randomly produces a sequence j, that is seq=j, then this package would be send back. After that sever status is setted to SYN-RECV status.
* **Three-Way Handshake**:
When Client receives the package, it will check the acknowledgement sequence's value and the ACK bit. If ACK=1 and seq=i+1, it will send the last package of the Three-Way Handshake. The ACK bit also is setted as 1 and the seq=j+1, then this package is sent to server by client. Once this package leaves away client, it state is setted as ESTAB-LISHED which represents that the TCP connection has been established successfully. Then, the client and server can tranfer datas each other.
## 3. Four-Way Wavehand
Four-Way Wavehand refers to that close a TCP connection require four package between client and server in total. The reson why close a TCP connection needs four package is that TCP connection is a Full Duplex data transfers channel. Thus, client and server must close respectively, called half-close, to avoid loss of data.

![18.JPG](/images/18.png)


Duing to TCP connection is full duplex data transfers channel, each channel must be closed one by one to avoid loss of data. For example, when Client hasn't data to transfrom, the client will send a package with FIN=1 to tell Server that I do not send any package, but I can receive package. After the server receives this package, it will response a package to tell client that I know you will not send package anymore, and keep sending package to client util data finish.

* **One-Way Wavehand**:
Client sends a package with FIN=1, seq=i to colse the data send channel, and then Clinet change to FIN-WAIT1 state.
* **One-Way Wavehand**:
After Servre receives the package with FIN=1, it sends a package with ACK=1,seq=i+1 to acknowledge received package, then changes to CLOSE-WAIT state.
* **One-Way Wavehand**:
After Servre sends over it's data, it will send a package with FIN=1, seq=j and changes it state to LAST-ACK.

* **One-Way Wavehand**:
The Client sends the last ACK package to Server, and changes t o TIME-WAIT state, which waits to 2MSL to avoid loss of package. Then Client is closed, and when the servre receive the last ACK package, it will be colsed to.

* **Notes**:
    * **TIME-WAIT**: MSL is the longest time of IP package exists in the internet. As result of routing abnormal, many packages always get loss in internet. And routing abnormal always requires several minute to fix it. During routing abnormal , package may get loss and loop in routes, meanwhile, transmitting end may resend this package, and the lossed package is called lost duplicate or wandering duplicate. TCP protocol must be able to deal wandering duplicate correctly. Therefore, TIME-WAIT state exist in TCP protocol is resonable. Firstly, Reliable implement of TCP full duplex protocol termination. Secondly, Allows duplicate segments to disappear in the network.
## 4. Code For Linux
There is the flow chart of TCP connection and linux code.

![19.JPG](/images/19.jpg)


    //Client 
    #include<sys/socket.h>
    #include<time.h>
    #define MAXLEN 1024
    int main(int argc,char** argv){
        int sockfd;
        struct sockaddr_in servaddr;
        if(argc!=2)
            err_quit("usage: tcpli <IP address>");
        sockfd=socket(AF_INET,SOCK_STREAM,0);
        bzero(&servaddr,sizeof(servaddr));
        servaddr.sin_family = AF_INET;
        servaddr.sin_port=htons(13);
        inet_port(AF_INET,argv[1],&Servaddr.sin_addr);
        connect(sockfd,(SA*)&Servaddr,sizeof(servaddr));
        str_cli(stdin,sockfd);
        return 0;
    }
    
    void str_cli(FILE *fp,int sockfd){
        char sendline[MAXLEN],recvline[MAXLEN];
        while(fgets(sendline,MAXLEN,fp)!=NULL){
            write(sockfd,sendline,strlen(sendline));
            if(readline(sockfd,recvline,MAXLEN)==0)
                err_quit("str_cli:server terminated prematruely");
            fputs(recvline,stdout);
        }
    }
    
    //Server
    #include<sys/socket.h>
    int main(){
        int listenfd,connfd;
        pid_t child_pid;
        socklen_t clilen;
        struct sockaddr_in cliaddr,servaddr;
        listenfd=socket(AF_INET,SOCK_STREAM,0);
        bzero(&servaddr,sizeof(servaddr));
        servaddr.sin_family = AF_INET;
        servaddr.sin_addr.s_addr=htonl(INADDR_ANY);
        servaddr.sin_port=htons(13);
        bind(listenfd,(SA*)&servaddr,sizeof(servaddr));
        listen(listenfd,LISTENQ);
        for(;;){
            clilen=sizeof(cliaddr);
            connfd=accept(listenfd,(SA*)&cliaddr,&clilen);
            if((chilpid=fork()==0)){
                close(listenfd);
                str_echo(connfd);
                exit(0);
            }
            close(connfd);
        }
        return 0;
    }
    
    void str_echo(int sockfd){
        ssize_t n;
        char buf[MAXLEN];
        again:
            while((n=read(sockfd,buf,MAXLEN))>0)
                write(sockfd,buf,n);
            if(n<0&&errno=EINTR)
                goto again;
            else if(n<0)
                err_sys("str_echo: read error");
    }
## Reference
[1] https://www.cnblogs.com/Qing-840/p/9283367.html

[2] <<TCP/IP Illustrated, Volume 1: The Protocols>>

[3] <<UNIX network programming>>

