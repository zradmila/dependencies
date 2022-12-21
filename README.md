# Working with remote servers

## Theory [2]

* [0.4] What are [computer ports](https://www.cloudflare.com/learning/network-layer/what-is-a-computer-port/) on a high level? How many ports are there on a typical computer?  
A **port** is a virtual point where network connections start and end. Ports are software-based and managed by a computer's operating system. Each port is associated with a specific process or service. Ports allow computers to easily differentiate between different kinds of traffic: emails go to a different port than webpages, for instance, even though both reach a computer over the same Internet connection.  
* [0.4] What is the difference between http, https, ssh, and other protocols? In what sense are they similar? Name default ports for several data transfer protocols.

All of them are protocols to communicate between client and server.
The **Hypertext Transfer Protocol (HTTP)** is the foundation of the World Wide Web, and is used to load webpages using hypertext links. HTTP is an application layer protocol designed to transfer information between networked devices and runs on top of other layers of the network protocol stack. A typical flow over HTTP involves a client machine making a request to a server, which then sends a response message.  
**Hypertext transfer protocol secure (HTTPS)** is the secure version of HTTP, which is the primary protocol used to send data between a web browser and a website. HTTPS is encrypted in order to increase security of data transfer. This is particularly important when users transmit sensitive data, such as by logging into a bank account, email service, or health insurance provider.  
HTTPS uses an encryption protocol to encrypt communications. The protocol is called Transport Layer Security (TLS), although formerly it was known as Secure Sockets Layer (SSL). This protocol secures communications by using what’s known as an asymmetric public key infrastructure. This type of security system uses two different keys to encrypt communications between two parties: the private key and the public key.  
**SSH or Secure Shell** is a network communication protocol that enables two computers to communicate (c.f http or hypertext transfer protocol, which is the protocol used to transfer hypertext such as web pages) and share data. An inherent feature of ssh is that the communication between the two computers is encrypted meaning that it is suitable for use on insecure networks.

SSL(HTTPS) is used to encrypt communication between Browser and Server. Whereas SSH is used to encrypt communication between Two Computers. One computer securely accessing a remote computer → Copy Files Between Computers, host a git repo on another old computer in your house instead of using GitHub as your remote backup .
Port Number | Usage
------------|-------
20          | File Transfer Protocol (FTP) Data Transfer
21          | File Transfer Protocol (FTP) Command Control
22          | Secure Shell (SSH)
23          |Telnet - Remote login service, unencrypted text messages
25 | Simple Mail Transfer Protocol (SMTP) E-mail Routing
53 | Domain Name System (DNS) service
80 | Hypertext Transfer Protocol (HTTP) used in World Wide Web
110 | Post Office Protocol (POP3) used by e-mail clients to retrieve e-mail from a server
119 |Network News Transfer Protocol (NNTP)
123 | Network Time Protocol (NTP)
143 | Internet Message Access Protocol (IMAP) Management of Digital Mail
161 | Simple Network Management Protocol (SNMP)
194 | Internet Relay Chat (IRC)
443 | HTTP Secure (HTTPS) HTTP over TLS/SSL

* [0.4] Explain briefly: (1) what is IP, (2) what IPs are called 'white'/public, (3) and what happens when you enter 'google.com' into the web browser. 

(1) An IP address is a unique address that identifies a device on the internet or a local network. IP stands for "Internet Protocol," which is the set of rules governing the format of data sent via the internet or local network.  
(2) A public IP address identifies you to the wider internet so that all the information you’re searching for can find you. A private IP address is used within a private network to connect securely to other devices within that same network.  
(3) When we enter 'google.com' into the web browser, the following things happens:
- The browser checks the cache for a DNS record to find the corresponding IP address of maps.google.com.
DNS(Domain Name System) is a database that maintains the name of the website (URL) and the particular IP address it links to. Every single URL on the internet has a unique IP address assigned to it. The IP address belongs to the computer which hosts the server of the website we are requesting to access.
To find the DNS record, the browser checks four caches: browser cache, OS cache, router cache and ISP cache. 
-  If the requested URL is not in the cache, ISP’s DNS server initiates a DNS query to find the IP address of the server that hosts google.com.
- The browser initiates a TCP connection with the server.
- The browser sends an HTTP request to the webserver.
- The server handles the request and sends back a response.
- The server sends out an HTTP response.
- The browser displays the HTML content (for HTML responses, which is the most common)

* [0.4] What is Nginx? How does it work on the high level? List several alternative web servers.  
**NGINX** is an open source software for web serving, reverse proxying, caching, load balancing, media streaming, and more. It started out as a web server designed for maximum performance and stability. In addition to its HTTP server capabilities, NGINX can also function as a proxy server for email (IMAP, POP3, and SMTP) and a reverse proxy and load balancer for HTTP, TCP, and UDP servers.  
**Alternative web servers**: HAProxy, Traefik, Caddy
* [0.4] What is SSH, and for what is it typically used? Explain two ways to authenticate in an SSH server in detail.  
**SSH** is a cryptographic protocol for connecting to network services over an unsecured network. Common applications for SSH are remote login and remotely executing commands on Linux hosts, but that only scratches the surface of what you can do with SSH.  
The protocol covers 3 main areas.
- Authentication - authentication involves proving a user or a machine’s identity; that is, who they claim to be. For SSH, the authentication method means confirming that the user has the correct credentials to access the SSH Server.  
- Encryption - SSH is an encrypted protocol, so the data is unintelligible except to the intended recipients.  
- Integrity - SSH guarantees that the data traveling over the network arrives unaltered. If a third party was to modify traffic during transit, SSH would detect this.

## Problem [6.5]

A real-life situation that occurred to me several times over the years.

Imagine wrapping up a large bioinformatics project and wanting to share raw data with your colleagues in a friendly and straightforward format. The best option would be to use an online genome browser and host your data remotely, so it is easily accessible by anyone with a valid link. This is exactly what we will be doing here.

*Please consider doing this HW using Linux since setting up the SSH client on Windows is painful, and I won't be able to help you.*

**Remote Server**:
* [2] Create a new virtual machine in the Yandex/Mail/etc cloud (order at least 10GB of free disk space). Generate SSH key pair and use it to connect to your server.
* [1] Download the latest human genome assembly (GRCh38) from the Ensemble FTP server ([fasta](https://ftp.ensembl.org/pub/release-108/fasta/homo_sapiens/dna/Homo_sapiens.GRCh38.dna.primary_assembly.fa.gz), [GFF3](https://ftp.ensembl.org/pub/release-108/gff3/homo_sapiens/Homo_sapiens.GRCh38.108.gff3.gz)). Index the fasta using samtools (`samtools faidx`) and GFF3 using tabix. 
* [1] Select and download BED files for three ChIP-seq and one ATAC-seq experiment from the ENCODE (use one tissue/cell line). Sort, bgzip, and index them using tabix.

**JBrowse 2**
* [1] Download and install [JBrowse 2](https://jbrowse.org/jb2/). Create a new jbrowse [repository](https://jbrowse.org/jb2/docs/cli/#jbrowse-create-localpath) in `/mnt/JBrowse/` (or some other folder).
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

* [0.25] Restart the nginx (reload its config) and make sure that you can access the browser using a link like this: `http://64.129.58.13/jbrowse/`. Here `64.129.58.13` is your public IP address.
* [1] Add your files (BED & FASTA & GFF3) to the genome browser and verify that everything works as intended. Don't forget to [index](https://jbrowse.org/jb2/docs/cli/#jbrowse-text-index) the genome annotation, so you could later search by gene names.

**Remember to put a [persistent link](https://jbrowse.org/jb2/docs/user_guides/basic_usage/#sharing-sessions) to a JBrowse 2 session with all your BED files and the genome annotation in the report (like [this](https://jbrowse.org/code/jb2/v2.3.1/?session=share-HShsEcnq3i&password=nYzTU)). I must be able to access it without problems.**
-------
## Extra points [1.5]

* [1] Create a Docker container for running JBrowse 2. It should be a self-contained application, listening on the default HTTP port. Users must be able to mount directories with custom configs and access them later without any problems. 

Hint: to specify the config, use the config=PATH query parameter. E.g. `http://64.129.58.13/jbrowse/?config=my_folder%2Fconfig.json` where `my_folder%2Fconfig.json` is the [escaped](https://en.wikipedia.org/wiki/Percent-encoding) path to the config file.

* [0.5] Give an in-depth explanation of the OSI model and how the TCP/IP stack works. Don't copy-paste descriptions from the internet; paraphrase and shorten as much as possible (imagine writing a cheat sheet for yourself)
