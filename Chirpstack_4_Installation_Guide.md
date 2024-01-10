# Chirpstack v4 installation in Ubuntu

ChirpStack is an open-source LoRaWAN Network Server stack that facilitates the deployment and management of LoRaWAN networks. LoRaWAN (Long Range Wide Area Network) is a wireless communication protocol designed for low-power, long-range communication between IoT (Internet of Things) devices and gateways.


### Install Mosquitto, Redis, PostgreSQL.
- **mosquitto** : A lightweight MQTT (Message Queuing Telemetry Transport) broker.
- **mosquitto-clients** : Command-line MQTT clients for publishing and subscribing messages.
- **redis-server** : The Redis server, an in-memory data structure store.
- **redis-tools** : Redis command-line utilities.
- **postgresql**: The PostgreSQL relational database management system.
```bash
sudo apt install mosquitto mosquitto-clients redis-server redis-tools postgresql
```
### Configure PostgreSQL Database
Switch to the PostgreSQL command-line tool
```bash
sudo -u postgres psql
```
Create a role for authentication. The role you've created can be named according to your preference. Here it's named "chirpstack" and the password is also "chirpstack" .
```bash
create role chirpstack with login password 'chirpstack';
```

Create a database and give it a name, in this case, let's use "chirpstack" Make sure that the user "chirpstack" is set as the owner of this database.

```bash
create database chirpstack with owner chirpstack;
```
Switch to the "chirpstack" database

```bash
\c chirpstack
```

Create the pg_trgm extension. The pg_trgm module provides functions and operators for determining the similarity of alphanumeric text based on trigram matching, as well as index operator classes that support fast searching for similar strings.

```
create extension pg_trgm;
```

Exit the PostgreSQL command-line tool.

```
\q
```

### Install ChirpStack Gateway Bridge

```bash
sudo apt install apt-transport-https dirmngr
```

Add the ChirpStack repository key

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1CE2AFD36DBCCA00
```

Add the ChirpStack repository to the sources list

```bash
sudo echo "deb https://artifacts.chirpstack.io/packages/4.x/deb stable main" | sudo tee /etc/apt/sources.list.d/chirpstack.list
```

Update package information

```bash
sudo apt update
```

Install ChirpStack Gateway Bridge

```bash
sudo apt install chirpstack-gateway-bridge
```


### Configuration

Configure the ChirpStack Gateway Bridge by updating the MQTT integration section in the configuration file located at /etc/chirpstack-gateway-bridge/chirpstack-gateway-bridge.toml.

open the chirpstack-gateway-bridge file in nano editor for editing configuration.
```bash
sudo nano /etc/chirpstack-gateway-bridge/chirpstack-gateway-bridge.toml
```
Change the frequency plans based on your country.
```bash
#Example for EU868:
[integration.mqtt]
event_topic_template="eu868/gateway/{{ .GatewayID }}/event/{{ .EventType }}"
command_topic_template="eu868/gateway/{{ .GatewayID }}/command/#"
```
 Here EU868 is the frequency plan of Europe. Change the plan according to your country. To customize the frequency plan in the code, you should replace "eu868" in both the "event_topic_template" and "command_topic_template" with the specific identifier corresponding to your country's frequency plan.
 You can refer frequency plans of all the countries from the link mentioned below:
 "https://www.thethingsnetwork.org/docs/lorawan/frequencies-by-country/"


### Start ChirpStack Gateway Bridge

```bash
sudo systemctl start chirpstack-gateway-bridge
```

### Start ChirpStack Gateway Bridge on boot

```bash
sudo systemctl enable chirpstack-gateway-bridge
```

### Install ChirpStack

```bash
sudo apt install chirpstack
```

### Start ChirpStack

```bash
sudo systemctl start chirpstack
```

### Start ChirpStack on boot

```bash
sudo systemctl enable chirpstack
```

[ NOTE : ] Check ChirpStack logs if any error occurs while running the server.

```
sudo journalctl -f -n 100 -u chirpstack
```

Access ChirpStack web interface by going to **localhost:8080** in your browser

[ NOTE : ] The default login is admin as user, and the password is also admin.


### Rerefence
[Chirpstack v4 Installation Guide for Debian/Ubuntu](https://www.chirpstack.io/docs/getting-started/debian-ubuntu.html)
