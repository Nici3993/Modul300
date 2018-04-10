# Installation Docker

Docker bildet auf Basis von Linux-Systemen einen Container.
Dieser Container kann zum Beispiel einen Webserver oder einen MySQL-DB Server beinhalten.

## Docker installieren

1. Laden Sie die Dockertoolbox von https://store.docker.com/editions/community/docker-ce-desktop-windows herunter.
1. Starten Sie das Exe-File als Administrator.
1. Folgen Sie dem Menü.
1. Nach erfolgreicher Installation, starten Sie als erstes das neue Programm Kitematic (Alpha)
1. Kitematic richtet als erstes eine Basis VM ein, auf welcher alle notwendigen Docker-Tools installiert sind. Mit diesen Docker-Tools lassen sich die Containers aufbauen.

Das Programm Kitematic ist eine Gui-Version um Docker Container zu verwalten. In diesem Modul lernen wir aber Docker mittels Scripts zu verwalten.

## Docker mit PowerShell Administrieren

PowerShell ist die geignete Konsole auf Windows-Hosts um Docker-Containers zu administrieren.

Folgende Commands sind hilfreich bei dem Management von Docker:

```powershell
Management Commands:
  config      Manage Docker configs
  container   Manage containers
  image       Manage images
  network     Manage networks
  node        Manage Swarm nodes
  plugin      Manage plugins
  secret      Manage Docker secrets
  service     Manage services
  swarm       Manage Swarm
  system      Manage Docker
  trust       Manage trust on Docker images
  volume      Manage volumes
Commands:
  attach      Attach local standard input, output, and error streams to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  images      List images
  import      Import the contents from a tarball to create a filesystem image
  info        Display system-wide information
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  login       Log in to a Docker registry
  logout      Log out from a Docker registry
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  run         Run a command in a new container
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  version     Show the Docker version information
  wait        Block until one or more containers stop, then print their exit codes

```

## Infrmationen zum Docker-Client und zur Docker-Engine auf dem NB

```PowerShell
PS C:\Users\nicola.alig> docker version
Client:
 Version:	18.03.0-ce
 API version:	1.37
 Go version:	go1.9.4
 Git commit:	0520e24
 Built:	Wed Mar 21 23:06:28 2018
 OS/Arch:	windows/amd64
 Experimental:	false
 Orchestrator:	swarm

Server:
 Engine:
  Version:	18.03.0-ce
 API version:	1.37 (minimum version 1.12)
  Go version:	go1.9.4
  Git commit:	0520e24
  Built:	Wed Mar 21 23:14:32 2018
  OS/Arch:	linux/amd64
  Experimental:	false
```

## Docker-Container erstellen (Ubuntu 14.04)

- Zu erst wird das Docker-Image für den Container heruntergeladen.

```PowerShell
docker search ubuntu
```

- Anschliessend wählen wir das gewünschte Docker-Image mit dem Befehl docker pull ubuntu:14.04 heruntergeladen.

```PowerShell
docker pull ubuntu:14.04
Pulling repository ubuntu
ad892dd21d60: Download complete
511136ea3c5a: Download complete
e465fff03bce: Download complete
23f361102fae: Download complete
9db365ecbcbb: Download complete
```

- Mit folgendem Command wird überprüft, ob das Image Heruntergeladen wurde.

```PowerShell
docker images
```

Es werden alle Heruntergeladenen Images angezeigt.

- Nun Verbinden wir uns auf den Docker Container mit folgendem Befehl: (Als Name verwendet man die ImageID)

```PowerShell
docker run -ti Docker_NAME:Version /bin/bash
```

In diesem Fall sieht dies so aus:

```PowerShell
docker run -i -t ad892dd21d60  /bin/bash
```

## Mein Dockerfile

Über dieses Docker-File kann ein Container automatisch gestartet werden.

- Gehe davor mit über das CMD in das entsprechende Directory.
- Speichere dort das Docker-Config-File mit dem Namen dockerfile ab.
- Starte den Prozess mit dem Befehl

Dabei ist container4 der Name des Containers und die 4 nach dem Doppelpunkt der Docker Tag.

```Ruby
docker build -t container4:4 .
```

```Ruby
FROM ubuntu:latest

#New Repository
CMD ["bash"]
RUN echo "deb http://security.ubuntu.com/ubuntu trusty-security main universe" >> /etc/apt/sources.list

#Updates installieren
RUN apt-get update -y
RUN apt-get -y upgrade

#Installation Apache2
RUN apt-get -y install apache2

#Security
#Firewall (ufw inkl. Konfiguration)
RUN apt-get -y install ufw
RUN ufw enable
RUN ufw allow 80/tcp
#RUN ufw allow 3306/tcp

#Neuer User, welcher in die Dockergroupe "G-Docker-Members" kommt
#Erhöht die Sicherheit, damit nicht immer der Root-User verwendet werden muss!

RUN groupadd -r G-Docker-Members && useradd -r -g G-Docker-Members userfordocker

#Change Abache-Config (um zu verhindern, dass Apache nicht verändert werden kann.)
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

# Webseite herunterladen --> Ersetzt aktuelle Seite durch eine andere

RUN rm /var/www/html/index.html
RUN wget  -O /var/www/ "https://alighome01.myqnapcloud.com/share.cgi/index.html?ssid=0eyfCQS&fid=0eyfCQS&open=normal&ep="

EXPOSE 80

#Start apache2
CMD /usr/sbin/apache2ctl -D FOREGROUND
```

Das obige Docker-File ist ähnich wie das Vagrant-File aufgebaut. Zu erst wird der Container definiert Linie 148.
Anschliessend wird über Linux-Befehle der Container mit den notwendigen Features ausgestatet.

## Ressourcen Beschränkung der Docker Container

Damit die Container nicht unendlich Ressourcen in Anspruch nehmen, wird mittels des nächsten Befehls die RAM und CPU eingeschränkt.

Memory-Einschränkung:

```Ruby
docker run -m 1024m --memory-swap 2018m -t ubuntu:14:04
```

CPU-Einschränkung:

```Ruby
docker run -it --cpus=".5" ubuntu:14:04
```

Detailliertere Informationen zu allen Einschränkungen sind hier zu finden:

https://docs.docker.com/config/containers/resource_constraints/

## Docker Monitoring

Monitoring ist aufgrund der sehr hohen Anforderungen an die IT-Infrastruktur extrem wichtig.
Der Admin muss jeder zeit wissen wie es seiner Infrastruktur geht. Sprich die Komplette Auslastung muss irgendwo erfasst wreerden und wenn Fehler auftretten müssen die Administratoren benachrichtigt werden.

Gemäss Doku kann dies mit folgendem Befehl am einfachsten realisiert werden:

```Ruby
docker run -d --name cadvisor -v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys:ro -v /var/lib/docker/:/var/lib/docker:ro -p 8880:8880 google/cadvisor:latest
```

Den Port der Webseite habe ich auf 8880 angepasst.