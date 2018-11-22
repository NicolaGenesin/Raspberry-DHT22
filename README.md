# Raspberry-DHT22

This repo contains everything you need to set up the DHT22 (or DHT11) sensor with the Raspberry PI.

## Basic Usage

    sudo apt-get install build-essential python-dev python-openssl -y
    git clone https://github.com/NicolaGenesin/Raspberry-DHT22.git
    cd Raspberry-DHT22
    sudo python setup.py install
    # 22 (for the DHT22 driver) and 4 (to use gpio 4)
    sudo python examples/basic.py 22 4

The output will be something like 

    Temp=22.2*  Humidity=42.9%

## Bonus (InfluxDB & Grafana)

Let's check your os release

    lsb_release -a

If you are using jessie or Debian 8.0
    
    sudo apt-get update && sudo apt install apt-transport-https curl
    curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
    echo "deb https://repos.influxdata.com/debian jessie stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
    sudo apt-get update && sudo apt-get install influxdb

If you are using wheezy or Debian 7.0
    
    sudo apt-get update && sudo apt install apt-transport-https curl
    curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
    echo "deb https://repos.influxdata.com/debian wheezy stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
    sudo apt-get update && sudo apt-get install influxdb

Finally start the service and create an empty database

    sudo service influxdb start
    influx
    create database "sensor_data" 

From another terminal tab, let's install Grafana

    sudo apt-get install apt-transport-https curl
    curl https://bintray.com/user/downloadSubjectPublicKey?username=bintray | sudo apt-key add -
    echo "deb https://dl.bintray.com/fg2it/deb jessie main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
    sudo apt-get update
    sudo apt-get install grafana
    sudo service grafana-server start
    # The default HTTP port is 3000 and default user/password is admin.
    # (Optional) To configure the Grafana server to start at boot time:
    sudo update-rc.d grafana-server defaults

Let's run the bonus.py script!

    pip install influxdb
    # bonus.py is very similar to bonus.py with the difference that it writes to the influx database to create date for Grafana 
    sudo python examples/bonus.py 22 4

The output will be something like this, every 5 seconds

    Temp=22.2*  Humidity=42.9%
    .
    .


Back to the database tab, if you query it

    select * from "rpi"

The output will be something like this

    # time, humidity (%), location, temperature (C)
    1542843259000000000 42.400001525878906 living room 22.100000381469727

Now all you need to do is to open Grafana at localhost:3000 and configure your dashboard :)
