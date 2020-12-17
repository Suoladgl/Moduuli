 # Esitys
Moduulin tarkoituksena on luoda teamspeak käyttäjä johon asennetaan teamspeak3 server ja hyväksytään automaattisesti ehdot ja käynnistetään palvelu.
Moduuli perustuu oletukseen, että käyttöliittymänä on xubuntu-18.04.

 # init.sls
 
    
        teamspeakuser:
          user.present:
            - name: teamspeak
        extract:
          archive.extracted:
            - name: /home/teamspeak/
            - source: https://files.teamspeak-services.com/releases/server/3.13.2/teamspeak3-server_linux_amd64-3.13.2.tar.bz2
            - source_hash: ffb6c8cc222228eaaed79930ebd39fbf2f8a6d557d1a67d7eafc5e7e8c4931d1
            - user: teamspeak
            - group: teamspeak
        /home/teamspeak/teamspeak3-server_linux_amd64/.ts3server_license_accepted:
          file.managed:
            - source: salt://teamspeak/.ts3server_license_accepted
            - user: teamspeak
            - group: teamspeak
        /lib/systemd/system/teamspeak.service:
          file.managed:
            - source: salt://teamspeak/teamspeak.service
        teamspeakservice:
          service.running:
            - name: teamspeak
            - enable: True
        test.sleep:
          module.run:
            - length: 3
        checkstatusgettoken:
          cmd.run:
            - name: 'systemctl status teamspeak'
            
            
 # teamspeak.service
 
        [Unit]
        Description=TeamSpeak 3 Server
        After=network.target
        [Service]
        WorkingDirectory=/home/teamspeak/teamspeak3-server_linux_amd64/
        User=teamspeak
        Group=teamspeak
        Type=forking
        ExecStart=/home/teamspeak/teamspeak3-server_linux_amd64/ts3server_startscript.sh start inifile=ts3server.ini
        ExecStop=/home/teamspeak/teamspeak3-server_linux_amd64/ts3server_startscript.sh stop
        PIDFile=/home/teamspeak/teamspeak3-server_linux_amd64/ts3server.pid
        RestartSec=15
        Restart=always
        [Install]
        WantedBy=multi-user.target
            
![downloads](./teamspeakdownloads.PNG)
