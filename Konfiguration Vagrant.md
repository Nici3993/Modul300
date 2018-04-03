# Konfiguration Vagrant

Vagrant dient dazu, virtuelle Maschienen schnell, einfach und vorallem automatisch auf zusetzten.

## Beispiel Vagrant-File

Folgendes Vagrant-File kann für die Installation von Ubuntu Xenial benutzt werden.

```Ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/trusty64"
  config.vm.hostname = "Ubuntu64bit260318"
  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  config.vm.network "private_network", type: "dhcp"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
   config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
     vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.memory = "2048"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
     apt-get update
     apt-get upgrade -y
     apt-get install -y apache2
     apt-get install -y mysql-server
     mysql -u root -p tbz
     apt-get install -y ufw gufw
     apt-get autoremove -y
     ufw enable
     ufw allow 80/tcp
     ufw allow 3306/tcp

   SHELL
end

```

## Vagrant VM aufsetzten

Nun muss aus dem Vagrant File noch eine VM werden.

- Starte das Tool GitBash
- Wechsle in das Verzeichnis, in welchem du die VM und die Konfig möchtest.
- lege dort das Vagrant-File mit dem Namen "Vagrant" ab.
- Gib auf der Konsole folgenden Befehl ein:

```Ruby
vagrant init
```

- Das Vagrant File wird eingelesen.
- Fahre mit folgendem Befehl weiter:

```Ruby
vagrant up
```

- Die VM wird nun aufgestartet und die definierten Services installiert.

## Testen der Installation

- Öffne das Fenster der VM.
- Melde dich mit Vagrant vagrant an.
- Öffne die IP-Config mittels:

```Bash
ifconfig
```

- Gib die IP im Browser des Hostsystems ein.
- Falls die ApacheWebseite erscheint, hat die Installation geklappt.

## Erweiterte Packete

Es wird ebenfalls das Packet mySQL und die Linux Firewall "uncomplicated firewall" (ufw). Diese Firewall dient dazu, den Traffic auf der Netzwerkkarte zu limitieren.

Funktionsweise:

- Installation Firewall
- Als erstes wird die Firewall durch folgendes Packet installiert:

```Bash
apt-get install -y ufw
```

- Anschliessend durch den folgenden Befehl aktiviert.

```Bash
ufw enable
```

- Zuletzt werden die Firewallregeln nach den Bedürfnisen des Administrtors eingerichtet.

```Bash
ufw allow 80/tcp
```

- Für den mySQL-Server gilt das gleiche. Dieser wird ebenfalls über ein PAcket installiert (mysql-server).
- Um den SQL-Server von Remote managen zu können, muss ebenfalls ein Firewallport geöffnet werden (Port 3306).
- Somit ist die Installation vom Apache2-Webserver, dem Datenbank-Server mySQL, der Firewall ufw abgeschlossen. Die Firewall wurde ebenfalls entsprechend konfiguriert.

## Testing

### Webserver

Die funktionalität des Webservers wird mit folgenden zwei Tests begründet:

- Aufrufen der Apache-Standard-Webseite
- Prozess anzeigen, welcher den Apache benötigt

Screenshot Standardseite des Apache-Webserver:

![Alt-Text](https://alighome01.myqnapcloud.com/share.cgi/apache_Default.png?ssid=0agex3y&fid=0agex3y&path=%2F&filename=apache_Default.png&openfolder=normal&ep=)

Screenshot der laufenden Apache-Prozesse

![Alt-Text](https://alighome01.myqnapcloud.com/share.cgi/apache_prozesse.png?ssid=0agex3y&fid=0agex3y&path=%2F&filename=apache_prozesse.png&openfolder=normal&ep=)

### MySQL

#### MySQL Login-Page

![Alt-Text](https://alighome01.myqnapcloud.com/share.cgi/mysql_loginpage.png?ssid=0okiiNo&fid=0okiiNo&path=%2F&filename=mysql_loginpage.png&openfolder=normal&ep=)

#### MySQL Prozesse

![Alt-Text](https://alighome01.myqnapcloud.com/share.cgi/mysqlprozesse.png?ssid=0okiiNo&fid=0okiiNo&path=%2F&filename=mysqlprozesse.png&openfolder=normal&ep=)