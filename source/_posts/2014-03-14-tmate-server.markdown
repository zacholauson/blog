---
layout: post
title: "tmate Server"
date: 2014-03-14 17:13

---
I really enjoy pair programming, and I have been looking for a good way to pair remotely. Recently I came across [tmate](http://tmate.io/) which is a fork of tmux setup for easy pairing.
Its super easy to use but using its default setup it connects to their servers to setup the shared session, and I dont really like relying on their servers for my pairing so I decided to setup a DigitalOcean droplet to host my pairing sessions.

I used a Ubuntu 13.04 x32 droplet with 512mb of ram, which seems to be enough.

#### Install libraries to compile server:

{% codeblock %}
apt-get install git-core build-essential pkg-config libtool libevent-dev libncurses-dev zlib1g-dev automake libssh-dev cmake ruby
{% endcodeblock %}

#### Download tmate slave and make

{% codeblock %}
git clone https://github.com/nviennot/tmate-slave.git
cd tmate-slave
./create_keys.sh # remember the keys created from this
./autogen.sh
./configure
make
{% endcodeblock %}

#### Run server

You should be able to run:

{% codeblock %}
./tmate-slave
{% endcodeblock %}

Which should start the server on port 22

#### Setup client

To allow your tmate client to connect to your new server, you need to create a `~/.tmate.conf` file and include the ip or domain name for your server, the port you started it on, and the keys that were generated from `./create-keys.sh`

{% codeblock %}
set -g tmate-server-host              " "
set -g tmate-server-port              22
/   keys from ./create-keys.sh go here  /
set -g tmate-server-dsa-fingerprint   " "
set -g tmate-server-rsa-fingerprint   " "
set -g tmate-server-ecdsa-fingerprint " "
{% endcodeblock %}

#### Connect

Assuming the server is running and `~/.tmate.conf` was configured properly, you should be able to run `tmate` on your client and it should create a shared session using your server.


## Auto start tmate slave

Because I dont pair all of the time, theres no reason to have the server running all the time, so I setup a shell script to start the server and a cronjob to run the script when the server starts.

#### Shell script to start server

{% codeblock lang:sh %}
cd tmate-slave/
sudo ./tmate-slave -p 8000 # use -p to change port it starts on
{% endcodeblock %}

#### Cronjob

{% codeblock lang:sh %}
crontab -e
{% endcodeblock %}

Then add the entry to run the script on boot to the bottom of the opened file

{% codeblock lang:sh %}
@reboot ~/start-tmate.sh
{% endcodeblock %}

Now if you reboot your droplet, and run `ps aux | grep tmate` you should see that tmate-slave is running

## Extra Automation

DigitalOcean has a great api that allows you to automate server tasks.
I wrote a simple Ruby script that I could run to spin up the vps that is hosting my tmate slave and then open up tmate once the server is up.

{% codeblock lang:ruby %}
#!/usr/bin/env ruby

require 'json'
require 'yaml'

DIGITALOCEANFILE = "~/.digitalocean"
CONFIG           = YAML.load_file(File.expand_path(DIGITALOCEANFILE))

def get_dropletID(domain)
  CONFIG["droplets"][domain]
end

def boot_server(dropletID)
  JSON.parse(`curl -sG "https://api.digitalocean.com/droplets/#{dropletID}/power_on/?client_id=#{CONFIG["keys"]["client"]}&api_key=#{CONFIG["keys"]["api"]}"`)
end

def server_up?(dropletID)
  JSON.parse(`curl -sG "https://api.digitalocean.com/droplets/#{dropletID}?client_id=#{CONFIG["keys"]["client"]}&api_key=#{CONFIG["keys"]["api"]}"`)["droplet"]["status"] == "active"
end

def setup_pair_session_on(domain)
  dropletID = get_dropletID(domain)
  unless server_up?(dropletID)
    boot_server(dropletID)
    puts "waiting for server to boot"
    until server_up?(dropletID)
      sleep(10)
    end
    puts "server up, connecting..."
    sleep(15)
  end
  `tmate`
end

setup_pair_session_on("pair.zacholauson.io")
{% endcodeblock %}

To keep my api keys and droplet ids out of my script, I put then in a yaml config, which is loaded on lines 6 and 7 of my script.

{% codeblock lang:yaml %}
# ~/.digitalocean

keys:
  client: abcdefghijklmnopqrstuv
  api:    123456789abcdefghijklmnopqrstuv
droplets:
  sampledomain.com:            1234567
  sub.sampledomain.com:        1234567
  anothersub.sampledomain.com: 1234567
{% endcodeblock %}

Still on my todo list is to pipe the ssh command generated from tmate back to the clipboard so I can easily send it to whoever I am pairing with.
