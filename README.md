#ZABBIX 3.0 Monitor server TCP connection status


#Monitoring principle
1、TCP connection can be obtained using commands:
*  netstat -an | awk '/ ^ tcp / {++ S [$ NF]} END {for (a in S) print a, S [a]}'

2、You can use man netstat to view various descriptions of TCP status information:
2.1. LISTEN - Listen for connection requests from remote TCP ports； 
2.2. SYN-SENT - Waiting for a matching connection request after sending a connection request； 
2.3. SYN-RECEIVED - Waiting for confirmation of a connection request after receiving and sending a connection request； 
2.4. ESTABLISHED- Represents an open connection, data can be transmitted to the user； 
2.5. FIN-WAIT-1 - Waiting for a remote TCP connection interrupt request or confirmation of a 
	             previous connection interrupt request；
2.6. FIN-WAIT-2 - Waiting for connection interruption request from remote TCP； 
2.7. CLOSE-WAIT - Waiting for a connection interruption request from a local user； 
2.8. CLOSING -    Waiting for remote TCP to acknowledge connection interruption； 
2.9. LAST-ACK -   Waiting for confirmation of the original connection interruption request to the remote TCP； 
2.10.TIME-WAIT -  Wait enough time to ensure that the remote TCP 
	             receives an acknowledgement of the connection interruption request； 
2.11. CLOSED - No connection status；

##Instructions
    1、Place the status_TCP.conf file in the /etc/zabbix/zabbix_agentd.d directory.

    2、Place the script tcp_status.sh under the directory / etc / zabbix / scripts. If the directory does not exist, create the directory; grant the script execution permissions and add the execution permissions and the owner and group of tcp_status.
		*# mkdir /etc/zabbix/scripts
		*# chmod +x /etc/zabbix/scripts/tcp_status.sh
		*# chown zabbix:zabbix /etc/zabbix/scripts/tcp_status.sh

    3、Because the script stores some tcp information under / tmp /, in order for zabbix to read, we set the permissions that zabbix can read:
		*# touch /tmp/tcp_status.txt
		*# chown zabbix:zabbix /tmp/tcp_status.txt

    4、Restart zabbix agent
		*# service zabbix-agent restart

    5、Import templates in zabbix server (zabbix version> = 3.0, versions below 3.0 may not be imported): Template_TCP_Status_templates.xml

    6、Check KEY:
		*# zabbix_get -s 127.0.0.1  -k tcp.status[established]
			270
		*# zabbix_get -s 127.0.0.1  -k tcp.status[lastack]
			0
		*# zabbix_get -s 127.0.0.1  -k tcp.status[listen]
			11

    7, You can view server-side logs:：
		*# tailf /var/log/zabbix/zabbix_server.log

		 	21178:20171124:174831.855 item "Zabbix server:tcp.status[closed]" became supported
 			21185:20171124:174832.855 item "Zabbix server:tcp.status[closewait]" became supported
 			21178:20171124:174833.860 item "Zabbix server:tcp.status[closing]" became supported
 			21185:20171124:174834.861 item "Zabbix server:tcp.status[established]" became supported
			21178:20171124:174835.865 item "Zabbix server:tcp.status[finwait1]" became supported
 			21185:20171124:174836.866 item "Zabbix server:tcp.status[finwait2]" became supported
 			21178:20171124:174837.871 item "Zabbix server:tcp.status[lastack]" became supported
 			21185:20171124:174838.873 item "Zabbix server:tcp.status[listen]" became supported
 			21178:20171124:174839.877 item "Zabbix server:tcp.status[synrecv]" became supported
 			21185:20171124:174840.878 item "Zabbix server:tcp.status[synsent]" became supported
 			21178:20171124:174841.882 item "Zabbix server:tcp.status[timewait]" became supported




###参考文档
	1、http://www.xuliangwei.com/xubusi/637.html
	2、http://blog.csdn.net/reblue520/article/details/52274354
	3、https://www.abcdocker.com/abcdocker/2652
