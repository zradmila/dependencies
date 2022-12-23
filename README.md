# Working with remote servers

## Theory [2]

* [0.4] What are [computer ports](https://www.cloudflare.com/learning/network-layer/what-is-a-computer-port/) on a high level? How many ports are there on a typical computer?  

A **port** is a virtual point where network connections start and end. Ports are software-based and managed by a computer's operating system. Each port is associated with a specific process or service. Ports allow computers to easily differentiate between different kinds of traffic: emails go to a different port than webpages, for instance, even though both reach a computer over the same Internet connection.  
* [0.4] What is the difference between http, https, ssh, and other protocols? In what sense are they similar? Name default ports for several data transfer protocols.

All of them are protocols to communicate between client and server.
The **Hypertext Transfer Protocol (HTTP)** is the foundation of the World Wide Web, and is used to load webpages using hypertext links. HTTP is an application layer protocol designed to transfer information between networked devices and runs on top of other layers of the network protocol stack. A typical flow over HTTP involves a client machine making a request to a server, which then sends a response message.  

**Hypertext transfer protocol secure (HTTPS)** is the secure version of HTTP, which is the primary protocol used to send data between a web browser and a website. HTTPS is encrypted in order to increase security of data transfer. This is particularly important when users transmit sensitive data, such as by logging into a bank account, email service, or health insurance provider. 

**SSH or Secure Shell** is a network communication protocol that enables two computers to communicate (c.f http or hypertext transfer protocol, which is the protocol used to transfer hypertext such as web pages) and share data. An inherent feature of ssh is that the communication between the two computers is encrypted meaning that it is suitable for use on insecure networks.

SSL(HTTPS) is used to encrypt communication between Browser and Server. Whereas SSH is used to encrypt communication between Two Computers. One computer securely accessing a remote computer → Copy Files Between Computers, host a git repo on another old computer in your house instead of using GitHub as your remote backup .  

Port Number | Usage
------------|-------
20          | File Transfer Protocol (FTP) Data Transfer
21          | File Transfer Protocol (FTP) Command Control
22          | Secure Shell (SSH)
53 | Domain Name System (DNS) service
80 | Hypertext Transfer Protocol (HTTP) used in World Wide Web
110 | Post Office Protocol (POP3) used by e-mail clients to retrieve e-mail from a server
143 | Internet Message Access Protocol (IMAP) Management of Digital Mail
443 | HTTP Secure (HTTPS) HTTP over TLS/SSL

* [0.4] Explain briefly: (1) what is IP, (2) what IPs are called 'white'/public, (3) and what happens when you enter 'google.com' into the web browser. 

(1) An IP address is a unique address that identifies a device on the internet or a local network. IP stands for "Internet Protocol," which is the set of rules governing the format of data sent via the internet or local network.  
(2) A public IP address identifies you to the wider internet so that all the information you’re searching for can find you. A private IP address is used within a private network to connect securely to other devices within that same network.  
(3) When we enter 'google.com' into the web browser, the following things happens:
- The browser checks the cache for a DNS record to find the corresponding IP address of maps.google.com.
DNS(Domain Name System) is a database that maintains the name of the website (URL) and the particular IP address it links to. To find the DNS record, the browser checks four caches: browser cache, OS cache, router cache and ISP cache. 
-  If the requested URL is not in the cache, ISP’s DNS server initiates a DNS query to find the IP address of the server that hosts google.com.
- The browser initiates a TCP connection with the server.
- The browser sends an HTTP request to the webserver.
- The server handles the request and sends back a response.
- The server sends out an HTTP response.
- The browser displays the HTML content (for HTML responses, which is the most common)

* [0.4] What is Nginx? How does it work on the high level? List several alternative web servers.  

**NGINX** is an open source software for web serving, reverse proxying, caching, load balancing, media streaming, and more. It started out as a web server designed for maximum performance and stability. In addition to its HTTP server capabilities, NGINX can also function as a proxy server for email (IMAP, POP3, and SMTP) and a reverse proxy and load balancer for HTTP, TCP, and UDP servers. NGINX performs with an asynchronous, event-driven architecture: similar threads are managed under one worker process, and each worker process contains smaller units called **worker connections**. This whole unit is then responsible for handling concurrent requests. Worker connections deliver the requests to a worker process, which will also send it to the master process. Finally, the master process provides the result of those requests.    
**Alternative web servers**: HAProxy, Traefik, Caddy

* [0.4] What is SSH, and for what is it typically used? Explain two ways to authenticate in an SSH server in detail. 

**SSH** is a cryptographic protocol for connecting to network services over an unsecured network. Common applications for SSH are remote login and remotely executing commands on Linux hosts, but that only scratches the surface of what you can do with SSH.  
Two ways to authenticate in an SSH server:
    * Password authentication (using user name and passwords) - after establishing secure connection with remote servers, SSH users usually pass on their usernames and passwords to remote servers for client authentication. These credentials are shared through the secure tunnel established by symmetric encryption. The server checks for these credentials in the database and, if found, authenticates the client and allows it to communicate.
    * Public key-based authentication (using public and private key pairs) - after the client establishes a connection with the remote server, the client informs the server of the key pair it would like to authenticate itself with. The server verifies the existence of this key pair in its database and then sends an encrypted message to the client. The client decrypts the message with it’s private key and generates a hash value which is sent back to the server for verification. The server generates its own hash value and compares it with the one sent from the client. When both the hash values match, the server is convinced of the client’s authenticity and allows it to communicate with the server.  
    
## Problem [6.5]

A real-life situation that occurred to me several times over the years.

Imagine wrapping up a large bioinformatics project and wanting to share raw data with your colleagues in a friendly and straightforward format. The best option would be to use an online genome browser and host your data remotely, so it is easily accessible by anyone with a valid link. This is exactly what we will be doing here.

*Please consider doing this HW using Linux since setting up the SSH client on Windows is painful, and I won't be able to help you.*

**Remote Server**:
* [2] Create a new virtual machine in the Yandex/Mail/etc cloud (order at least 10GB of free disk space). Generate SSH key pair and use it to connect to your server.  
## Create a virtual machine via Yandex.Cloud
<img width="379" alt="vm" src="https://user-images.githubusercontent.com/49398049/209234852-3bc8da71-9d2a-4522-a616-e77a09d25abc.PNG">

SSH-key was generated by ssh-keygen, then public key was added in the virtual machine's settings.

To connect to the server, I used this command:
```
ssh -i key_file rpzaikina@51.250.40.42
```
where `key_file` contains the private key  

* [1] Download the latest human genome assembly (GRCh38) from the Ensemble FTP server ([fasta](https://ftp.ensembl.org/pub/release-108/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz), [GFF3](https://ftp.ensembl.org/pub/release-108/gff3/homo_sapiens/Homo_sapiens.GRCh38.108.gff3.gz)). Index the fasta using samtools (`samtools faidx`) and GFF3 using tabix.

The latest human genome assembly from Ensemble FTP server was downloaded:
```
wget https://ftp.ensembl.org/pub/release-108/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
wget https://ftp.ensembl.org/pub/release-108/gff3/homo_sapiens/Homo_sapiens.GRCh38.108.gff3.gz
```
To index fasta and GFF3, samtools and tabix were downloaded:
```
sudo apt-get install samtools
sudo apt-get install tabix
```
Then I unziped the downloaded archives and indexed them:
```
gzip -dk Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz
samtools faidx Homo_sapiens.GRCh38.dna.primary_assembly.fa

gzip -dk Homo_sapiens.GRCh38.108.gff3.gz
# this command was taken from tabix manual page
(grep "^#" Homo_sapiens.GRCh38.108.gff3; grep -v "^#" Homo_sapiens.GRCh38.108.gff3 | sort -t"`printf '\t'`" -k1,1 -k4,4n) | bgzip > Homo_sapiens.GRCh38.108.sorted.gff3.gz
tabix -p gff Homo_sapiens.GRCh38.108.sorted.gff3.gz
```
* [1] Select and download BED files for three ChIP-seq and one ATAC-seq experiment from the ENCODE (use one tissue/cell line). Sort, bgzip, and index them using tabix.

I chose the K562 cell line.
[ATAC-seq experiment](https://www.encodeproject.org/experiments/ENCSR868FGK/)
[ZN124 ChIP-seq](https://www.encodeproject.org/experiments/ENCSR124YFA/)
[ZNF77 ChIP-seq](https://www.encodeproject.org/experiments/ENCSR844MAB/)
[NR3C1 ChIP-seq](https://www.encodeproject.org/experiments/ENCSR980ZWE/)
```
wget https://www.encodeproject.org/files/ENCFF135AEX/@@download/ENCFF135AEX.bed.gz -O atac_seq.bed.gz
wget https://www.encodeproject.org/files/ENCFF881MMD/@@download/ENCFF881MMD.bed.gz -O zn124_chipseq.bed.gz
wget https://www.encodeproject.org/files/ENCFF809JNC/@@download/ENCFF809JNC.bed.gz -O znf77_chipseq.bed.gz
wget https://www.encodeproject.org/files/ENCFF201BGD/@@download/ENCFF201BGD.bed.gz -O nr3c1_chipseq.bed.gz
```
To sort bed files, I installed bedtools:
```
sudo apt install bedtools
```
All needed manupulations with bed files:
```
for i in znf77_chipseq zn124_chipseq nr3c1_chipseq atac_seq; do bedtools sort -i $i.bed > $i_sorted.bed ; bgzip $i_sorted.bed; tabix $i_sorted.bed.gz; done
```
Also I created bed files with the renamed first column (chromosome number) in order of a proper visualization in JBrowser:
```
for i in *_sorted.bed; do awk '{gsub(/^chr/,""); print}' $i > $(echo $i| cut -d '.' -f 1)'_renamed.bed'; done
# then ziped them and indexed
for i in *_sorted_renamed.bed; do bgzip -c $i > $i'.gz'; done
for i in *_sorted_renamed.bed.gz; do tabix -p bed $i; done
```

**JBrowse 2**
* [1] Download and install [JBrowse 2](https://jbrowse.org/jb2/). Create a new jbrowse [repository](https://jbrowse.org/jb2/docs/cli/#jbrowse-create-localpath) in `/mnt/JBrowse/` (or some other folder).

JBrowse installation looks like this:
```
sudo apt install npm
sudo npm install -g @jbrowse/cli
cd /mnt
sudo jbrowse create JBrowse
```
* [0.25] Install nginx and amend its config(/etc/nginx/nginx.conf) to contain the following section:
```conf
http {
  # Don't touch other options!
  # ........
  # ........

  # Comment this line(!):
  # include /etc/nginx/sites-enabled/*;

  # Add this:
  server {
    listen 80 default_server;
    index index.html;
    server_name _;

    # Don't put JBrowse inside the home directory!
    # You will have problems with permissions
    location /jbrowse/ {
      alias /mnt/JBrowse/;	
    }
  }
}
```
To install nginx, I followed this command:
```
sudo apt-get install nginx
```
and then I edited the nginx.conf
```
cd /etc/nginx
vim nginx.conf
```
<img width="516" alt="vim" src="https://user-images.githubusercontent.com/49398049/209240290-dc4d18ae-5c92-4540-8e65-2a6552507394.PNG">

* [0.25] Restart the nginx (reload its config) and make sure that you can access the browser using a link like this: `http://64.129.58.13/jbrowse/`. Here `64.129.58.13` is your public IP address.

I restart nginx by:
```
sudo nginx -s reload 
```
and I was able to access the browser using a link http://51.250.40.42/jbrowse/  

* [1] Add your files (BED & FASTA & GFF3) to the genome browser and verify that everything works as intended. Don't forget to [index](https://jbrowse.org/jb2/docs/cli/#jbrowse-text-index) the genome annotation, so you could later search by gene names. 

I add fasta with `add-assembly` option and GFF and BED files via `add-track` option.
```
sudo jbrowse add-assembly /home/rpzaikina/Homo_sapiens.GRCh38.dna.primary_assembly.fa --load copy --out /mnt/JBrowse/
sudo jbrowse add-track /home/rpzaikina/Homo_sapiens.GRCh38.108.sorted.gff3.gz --load copy --out /mnt/JBrowse/
sudo jbrowse add-track /home/rpzaikina/znf77_chipseq_sorted_renamed.bed.gz --load copy --out /mnt/JBrowse/
sudo jbrowse add-track /home/rpzaikina/zn124_chipseq_sorted_renamed.bed.gz --load copy --out /mnt/JBrowse/
sudo jbrowse add-track /home/rpzaikina/nr3c1_chipseq_sorted_renamed.bed.gz --load copy --out /mnt/JBrowse/
````
**Link to the final result:** http://51.250.40.42/jbrowse/?session=share-AeQmqjR54k&password=cSJHm
-------
-------
## Extra points [1.5]

* [1] Create a Docker container for running JBrowse 2. It should be a self-contained application, listening on the default HTTP port. Users must be able to mount directories with custom configs and access them later without any problems. 

Hint: to specify the config, use the config=PATH query parameter. E.g. `http://64.129.58.13/jbrowse/?config=my_folder%2Fconfig.json` where `my_folder%2Fconfig.json` is the [escaped](https://en.wikipedia.org/wiki/Percent-encoding) path to the config file.

* [0.5] Give an in-depth explanation of the OSI model and how the TCP/IP stack works. Don't copy-paste descriptions from the internet; paraphrase and shorten as much as possible (imagine writing a cheat sheet for yourself)

Open Systems Interconnection (OSI) model provides a standard for different computer systems to be able to communicate with each other (universal language for computer networking).
7 abstract layers, from the highest one to the lowest:  
**--- hardware layers ---** 

1. Application layer 
    * directly interacts with data from the user - software applications (web browsers, email clients, etc.) rely on the application layer to initiate communications
    * responsible for the protocols and data manipulation that the software relies on to present meaningful data to the user
    * application layer protocols include HTTP as well as SMTP (Simple Mail Transfer Protocol is one of the protocols that enables email communications)  
    
2. Presentation layer
    * preparation data so that it can be used by the application layer
      *  translation - communicating devices may be using different encoding methods
      *  encryption - if the devices are communicating over an encrypted connection, layer 6 is responsible for adding the encryption on the sender’s end as well as decoding the encryption on the receiver's end so that it can present the application layer with unencrypted, readable data.
      * compression - helps improve and efficiency by minimizing the size of transferring data.

3. Session layer 
    * opening and closing communication between the two devices
      * session -  the time between when the communication is opened and closed; the session layer ensures that the session stays open long enough to transfer all the data being exchanged, and then promptly closes the session in order to avoid wasting resources.
    * synchronizes data transfer with checkpoints
       * could set a multiple checkpoints to be able to resumed the session from the last checkpoint in the case of a disconnect or a crush
  
**--- heart of OSI ---**  

4. Transport layer  
    * end-to-end communication between the two devices
      *  taking data from the session layer and breaking it up into chunks called **segments** before sending it to layer 3
      *  the transport layer on the receiving device is responsible for reassembling the segments into data the session layer can consume.
    * flow control and error control
      * flow control determines an optimal speed of transmission to ensure that a sender with a fast connection does not overwhelm a receiver with a slow connection.       * error control on the receiving end by ensuring that the data received is complete, and requesting a retransmission if it isn’t. 
  
**--- software layers ---**

5. Network layer 
    * facilitates data transfer between **two different** networks. 
        *  if the two devices communicating are on the **same** network, then the network layer is unnecessary
    * breaks up segments from the transport layer into smaller units, called **packets**, on the *sender’s device*, and reassembling these packets on the receiving device. 
    * finds the best physical path for the data to reach its destination - routing.
    
6. Data link layer 
    * facilitates data transfer between two devices on the **same** network
    * takes packets from the network layer and breaks them into smaller pieces called **frames**. 
    * flow control and error control in **intra-network** communication
    sending cable --> bit stream --> receiving cable  
    
7. Physical layer 
    *  includes a physical equipment involved in the data transfer, such as the cables and switches
    *  data gets converted into a bit stream (string of 1s and 0s) 
    
The OSI model describes an idealized network communications with a family of protocols. TCP/IP does not correspond to this model directly. TCP/IP either combines several OSI layers into a single layer, or does not use certain layers at all.

TCP/IP is a network model describing the process of digital data transmission. TCP/IP provides end-to-end data communication specifying how data should be packetized, addressed, transmitted, routed, and received. This functionality is organized into four abstraction layers, an implementation of the layers for a particular application forms a **protocol stack**.
1. Link layer - communication methods for data that remains within a single network segment (link)  
2. Internet layer (IP) - internetworking between independent networks 
3. Transport layer (TCP) - host-to-host communication  
    * divides the message received from the session layer into segments and numbers them to make a sequence.
    * makes sure that the message is delivered to the correct process on the destination machine
    * makes sure that the entire message arrives without any error else it should be retransmitted.
4. Application layer - process-to-process data exchange for applications.

