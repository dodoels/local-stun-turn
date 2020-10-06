# local-stun-turn

## 1. Install Dependencies

- For Ubuntu

```
# install essential dependencies
sudo apt-get install build-essential 
sudo apt-get install openssl libssl-dev make

# install libevent
wget https://github.com/libevent/libevent/releases/download/release-2.1.10-stable/libevent-2.1.10-stable.tar.gz
tar -zxvf libevent-2.1.10-stable.tar.gz
cd libevent-2.1.10-stable
./configure
make & make install
cd ..

# install database dependencies (required by TURN)
sudo apt-get install sqlite libsqlite3-dev
sudo apt-get install mysql-server -y 
sudo apt-get install mysql-client -y
sudo apt-get -y install libmysqlcppconn-dev libmysqlclient-dev libmysql++-dev 
```

- For Windows

1. Install [Cygwin](https://www.cygwin.com/) and be 100% sure that `Devel` has been selected for installation (requires manual check) during the process. You should use Cygwin bash all the time in the following steps.

2. Download [libevent](http://libevent.org/) source file and unzip it, open Cygwin bash in the unzipped folder, then run `make && make install`

## 2. Install Coturn

Please make sure all the dependencies above are properly installed, especially databases. For Wind

```
git clone https://github.com/coturn/coturn.git
cd coturn
./configure
make && sudo make install
```


## 3. Create Admin Account

```
# create certificate, enter random info
openssl req -x509 -newkey rsa:2048 -keyout /etc/turn_server_pkey.pem -out /etc/turn_server_cert.pem -days 99999 –nodes

# create coturn admin account.
turnadmin -a -u [username] -p [password] -r [realm_name]
```

## 4. Configure Coturn

```
# create coturn config
sudo cp /usr/local/etc/turnserver.conf.default /usr/local/etc/turnserver.conf
```

For local usage, simply add the following configs to `turnserver.conf`

```
# the credentials you set above
user=[username]:[password]
# the realm you set above
realm=[realm_name]
Verbose
fingerprint
cli-password=1
cert=/etc/turn_server_cert.pem
pkey=/etc/turn_server_pkey.pem
lt-cred-mech
```

## 5. Run Coturn Stun and Turn

`turnserver -o -a -f -v -r [realm_name]`

## 6. Testing 

To test STUN

```
turnutils_stunclient 192.168.***.***
```

To test TURN

```
turnutils_uclient -u [username] -w [password] 192.168.***.***
```

## 7. Connect to WebRTC

Change you config to 

```
"webrtc": {
    "is_stun": false, // switch STUN and TURN
    "stun_server": "stun:192.168.***.***",
    "turn_server": "turn:192.168.***.***",
    "turn_username": "username",
    "turn_password": "password",
    "requires_auth": false,
    "sdp_max_message_size": 262144
},
```







