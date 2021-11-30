## Wireguard VPN with Docker

#### Create Droplet (Digital Ocean)

1. Create a Droplet with the following settings: 
* **Image:** Ubuntu 20.04 (LTS) x64
* **Plan:** Basic
* **CPU Option:** Regular Intel w/ SSD -> $5 per month
* **Authentication** SSH Keys \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a. Type "ssh-keygen" into terminal to generate a key pair \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;b. Enter a key name and passphrase \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;c. Type "cat *keyname.pub*" to display the public key name \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;d. Paste the displayed key into the DigitalOcean prompt

#### SSH into Droplet

1.	Click on the Droplet to view its IPv4 address.
2.	Type “ssh -i *PrivateKeyLocation* root@*DropletIPv4address*” into terminal to ssh into the Droplet. \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*Note: The -i specifies the path of the private key*

#### Install Docker
Link to website utilized: (https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

1.	Type “apt-get update” to update the package index
2.	To add Docker's official GPG key paste into the terminal: \
`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg` 
3.	To setup the stable repository paste into the terminal: \
`echo \` \
`"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \` \
`$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`
4.	Type “apt-get update” to update the package index (with the new information)
5.	Type “apt-get install docker-ce docker-ce-cli containerd.io” to install Docker \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*Note: To test that Docker was properly installed, type "docker run hello-world"*

#### Install Docker Compose
Link to website utilized: (https://docs.docker.com/compose/install/#install-using-pip) \
\
*Preface: I installed the package using pip because the curl version was not working correctly. The terminal was throwing an error that stated "unexpected token at `newline'." This was likely due to a bug.*

1.	Install pip by typing “apt install python3-pip”
2.	Install docker-compose by typing “pip install docker-compose” \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*Note: Can test successful installation by typing “docker-compose –version”*

#### Setup Wireguard 
Link to website utilized: (https://thematrix.dev/setup-wireguard-vpn-server-with-docker/) \

1.	To create the required Wireguard directories enter into the terminal: \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `mkdir -p ~/wireguard/` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `mkdir -p ~/wireguard/config/`
2.	Type “nano ~/wireguard/docker-compose.yml” to create/edit the yaml file. 
3.	Paste the following text into the file: \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `version: '3.8'` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `services:` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `wireguard:` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `container_name: wireguard` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `image: linuxserver/wireguard` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `environment:` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- PUID=1000` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- PGID=1000` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- TZ=Asia/Hong_Kong` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- SERVERURL=1.2.3.4` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- SERVERPORT=51820` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- PEERS=pc1,pc2,phone1` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- PEERDNS=auto` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- INTERNAL_SUBNET=10.0.0.0` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `ports:` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- 51820:51820/udp` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `volumes:` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- type: bind` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `source: ./config/` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `target: /config/` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- type: bind` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `source: /lib/modules` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `target: /lib/modules` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `restart: always` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `cap_add:` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- NET_ADMIN` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- SYS_MODULE` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `sysctls:` \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; `- net.ipv4.conf.all.src_valid_mark=1` \
\
4.	Change the “TZ” to “America/Chicago”
5.	Paste the DigitalOcean server’s IPv4 address under “ServerURL”
6.	Type “docker-compose up -d” while in the wireguard directory to build the server

#### Port-Forwarding (BONUS)

#### Connecting Cellphone to Wireguard
link to website utilized: (https://thematrix.dev/setup-wireguard-vpn-server-with-docker/) \

1. Type "docker-compose logs -f wireguard" to open the config files
2. Scan the *phone-related* peer QR code in the wireguard iOS/Android application

#### Connecting Computer to Wireguard

1.	Navigate to the previously created ~/wireguard/config/*peername* directory on the DigitalOcean server \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*Note: There should be three different peer directories (if default file settings were left as is)* \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*Note: The required configuration file is located within the corresponding peer directory. Each device that is connected to*  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*the wireguard VPN is considered to be a "peer," and therefore gets its own configuration file. In this case, I used the*   &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*"peer_pc1.conf" file that was located at ~wireguard/config/peer_pc1* 
2.	Retrieve corresponding config files from the DigitalOcean server (using Secure Copy Protocol) \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;a. Type “scp -i *PrivateKeyLocation* root@*DropletIPv4Address:ConfigFileLocation DesiredLocation*” 
3.	Open the Wireguard desktop application and hit “import from file” and choose the newly downloaded configuration file







