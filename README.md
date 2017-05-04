# Project-2-_Client
Attached Homework#2_Client
*\Client Project # 2
We choose the dest addr to be 255.255.255.255
set up a socket and keep waiting for user input
as when the user inputs it is broadcasted to the server/*
// client.c:
#include <sys/types.h>
#include <sys/socket.h>
#include <string.h>
#include <stdio.h>
#include <stdlib.h>
#include <netinet/in.h>
 
 
#define PORT 2089
// #define SRC_ADDR "172.16.1.120"
#define DEST_ADDR "255.255.255.255"
#define BUFSIZE 1024
int main(int argc, char *argv[])
{
    	int sockfd;
    	int broadcast=1;
    	struct sockaddr_in sendaddr;
    	struct sockaddr_in recvaddr;
    	int numbytes;
 
    	if((sockfd = socket(PF_INET,SOCK_DGRAM,0)) == -1)
    	{
            	perror("sockfd");
            	exit(1);
    	}
 
   	if((setsockopt(sockfd,SOL_SOCKET,SO_BROADCAST,
                            	&broadcast,sizeof broadcast)) == -1)
    	{
            	perror("setsockopt - SO_SOCKET ");
            	exit(1);
    	}
    	if((setsockopt(sockfd,SOL_SOCKET,SO_REUSEADDR,
                            	&broadcast,sizeof broadcast)) == -1)
    	{
            	perror("setsockopt - SO_SOCKET ");
            	exit(1);
    	}
 
    	sendaddr.sin_family = AF_INET;
    	sendaddr.sin_port = htons(PORT);
    	sendaddr.sin_addr.s_addr = INADDR_ANY;
    	memset(sendaddr.sin_zero,'\0',sizeof sendaddr.sin_zero);
 
    	if(bind(sockfd, (struct sockaddr*) &sendaddr, sizeof sendaddr) == -1)
    	{
            	perror("bind");
            	exit(1);
    	}
 
    	recvaddr.sin_family = AF_INET;
    	recvaddr.sin_port = htons(PORT);
    	recvaddr.sin_addr.s_addr = inet_addr(DEST_ADDR);
    	memset(recvaddr.sin_zero,'\0',sizeof recvaddr.sin_zero);
    	char buf[BUFSIZE];
    	bzero(buf, BUFSIZE);
    	printf("Please enter msg: ");
    	fgets(buf, BUFSIZE, stdin);
    	sprintf(buf, "%s%lu\n", buf,(unsigned long)time(NULL));
    	while((numbytes = sendto(sockfd, buf, BUFSIZE , 0,
    	(struct sockaddr *)&recvaddr, sizeof recvaddr)) != -1)
     	{
        	printf("Sent a packet\n");
        	bzero(buf, BUFSIZE);
        	printf("Please enter msg: ");
        	fgets(buf, BUFSIZE, stdin);
        	sprintf(buf, "%s%lu\n", buf,(unsigned long)time(NULL));
        	sleep(1000);
    	}    
    	perror("sendto");
    	exit(1);
 
 
    	close(sockfd);
 
    	return 0;
}

