# Installation
***

* __Install Mosquitto, Redis, PostgreSQL.__

```
sudo apt install mosquitto mosquitto-clients redis-server redis-tools postgresql
```
Mosquitto is an open-source message broker that implements the MQTT (Message Queuing Telemetry Transport) protocol. Redis is like a super-fast, super-smart memory storage system for computers. It's often used to store things that need to be accessed really fast, like website data or temporary information. PostgreSQL is an open-source relational database management system (RDBMS) that provides a powerful and extensible platform for efficiently storing, managing, and retrieving structured data.

* __Switch to the PostgreSQL command-line tool__

```
sudo -u postgres psql
```
* __Create a role for authentication. The role you've created can be named according to your preference. Here its named as chirpstack.__

```
create role chirpstack with login password 'chirpstack';
```

* __Make a database and give it any name; in this case, it's "chirpstack." Also, designate chirpstack as the owner.__

```
create database chirpstack with owner chirpstack;
```

* __Switch to the chirpstack database__

```
\c chirpstack
```

* __Create the pg_trgm extension. The pg_trgm module provides functions and operators for determining the similarity of alphanumeric text based on trigram matching, as well as index operator classes that support fast searching for similar strings.__

```
create extension pg_trgm;
```

* __Exit the PostgreSQL command-line tool__

```
\q
```

* __Install required packages for ChirpStack Gateway Bridge__

```
sudo apt install apt-transport-https dirmngr
```

* __Add the ChirpStack repository key__

```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 1CE2AFD36DBCCA00
```

* __Add the ChirpStack repository to the sources list__

```
sudo echo "deb https://artifacts.chirpstack.io/packages/4.x/deb stable main" | sudo tee /etc/apt/sources.list.d/chirpstack.list
```

* __Update package information__

```
sudo apt update
```

* __Install ChirpStack Gateway Bridge__

```
sudo apt install chirpstack-gateway-bridge
```


## Configuration
***

* __Once installed, ChirpStack needs to know some basic things to work correctly.__
* __Configure things like how devices connect, where data is stored, and how security is handled.__


* __Configure the ChirpStack Gateway Bridge by updating the MQTT integration section in the configuration file located at /etc/chirpstack-gateway-bridge/chirpstack-gateway-bridge.toml.__

```
#Example for EU868:
[integration.mqtt]
event_topic_template="eu868/gateway/{{ .GatewayID }}/event/{{ .EventType }}"
command_topic_template="eu868/gateway/{{ .GatewayID }}/command/#"
```
 Here EU868 is the frequency plan of Europe. Change the plan according to your country. To customize the frequency plan in the code, you should replace "eu868" in both the "event_topic_template" and "command_topic_template" with the specific identifier corresponding to your country's frequency plan.
 You can refer frequency plans of all the countries from the link mentioned below:
 "https://www.thethingsnetwork.org/docs/lorawan/frequencies-by-country/"


* __Start ChirpStack Gateway Bridge__

```
sudo systemctl start chirpstack-gateway-bridge
```

* __Start ChirpStack Gateway Bridge on boot__

```
sudo systemctl enable chirpstack-gateway-bridge
```

* __Install ChirpStack__

```
sudo apt install chirpstack
```

* __Configuration files for ChirpStack are located at /etc/chirpstack.__

* __Start ChirpStack__

```
sudo systemctl start chirpstack
```

* __Start ChirpStack on boot__

```
sudo systemctl enable chirpstack
```

* __Check ChirpStack logs if any error occurs while running the server.__

```
sudo journalctl -f -n 100 -u chirpstack
```

* __Access ChirpStack web interface by going to localhost:8080 in your browser__
* __The default login is admin as user, and the password is also admin.__

