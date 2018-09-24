Crear un systemd con watchdog - respawn
=======================================

.::
	[Unit]
	Description=wso2am server daemon
	Documentation=https://docs.wso2.com/
	After=network.target

	[Service]
	Environment="JAVA_HOME=/usr/java/jdk1.8.0_181-amd64"
	ExecStart=/bin/sh -c '/opt/wso2/wso2is-5.6.0/bin/wso2server.sh start'
	#ExecStop=/bin/sh -c '/opt/wso2/wso2is-5.6.0/bin/wso2server.sh stop'
	ExecStop=/bin/kill -SIGTERM $MAINPID
	PIDFile=/opt/wso2/wso2is-5.6.0/wso2carbon.pid
	KillMode=control-group
	User=wso2
	Group=wso2cluster
	Type=forking
	Restart=on-failure
	RestartSec=21

	[Install]
	WantedBy=multi-user.target
