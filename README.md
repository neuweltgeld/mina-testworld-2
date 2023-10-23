## Launching the Nodes
This guide is intended for Mina test-world-2 Block Producers. If you encounter a bug, please get in touch through the #testworld-2 channel in Discord.

```console
# First, set up and update the rampup Debian Repository for your platform.
sudo rm /etc/apt/sources.list.d/mina*.list

# auto read system config using $(lsb_release -cs)
echo "deb [trusted=yes] http://packages.o1test.net $(lsb_release -cs) rampup" | sudo tee /etc/apt/sources.list.d/mina.list
sudo apt-get update

# Then, install the package or packages that you need:
sudo apt-get install -y mina-berkeley=2.0.0rampup5-55b7818
```

```console
# If you are using Ufw, allow these permissions
sudo ufw enable
sudo ufw allow 22
sudo ufw allow 8302
sudo ufw allow 3089
```

```console
# After setting up your nodes you need to add your keys to your node.
# Please use the same keypair in all of your Network Performance Testing nodes.
# Do not share your keypairs with anyone.
# The keypair gives you access to an account on the testnet that has been allocated a stake.
# With this stake, you can start your block production on the testnet.

# Download the attached .zip file that contains your keys and password.
# On your server:

# Extract the .zip file xxx.zip
# Create a folder on your system to store the key files. We recommend ~/keys .
mkdir ~/keys

# Set the correct permissions.
chmod 700 ~/keys

# Create a file for your private key in ~/keys and add the contents of the extracted file xxx.zip to it.
# We recommend my-wallet
nano ~/keys/my-wallet

# Add contents of community-yyy-key
# Save the file

# Set the correct permissions.
chmod 600 ~/keys/my-wallet

# Create a file for your public key and add the contents of the file community-yyy.pub that you extracted.
# We recommend my-wallet.pub 
nano ~/keys/my-wallet.pub 

# Add contents of community-yyy.pub
# Save the file

# The password for your new wallet can be found in the file community-yyy-password.txt that you extracted. 

```


```console
# Next step is creating new libp2p keypair.
mina libp2p generate-keypair -privkey-path /root/keys/libkey

# Set a password. And this password will be yours MINA_LIBP2P_PASS
# MINA_PRIVKEY_PASS and UPTIME_PRIVKEY_PASS are same and
# can be found in the file community-yyy-password.txt that you extracted.

# Change the following "PASS" variables (do not delete double quotes) as above. 
RAYON_NUM_THREADS:6
UPTIME_PRIVKEY_PASS="PASS"
MINA_LIBP2P_PASS="PASS"
MINA_PRIVKEY_PASS="PASS"

# Create mina.env and paste variables in it. You can also use these variables by specifying them in the service file. 
# But make sure you use them in one place.
nano ~/.mina-env
```


```console
# Now, we will create a service file. This way mina will restart itself when it crashes.
sudo nano /etc/systemd/system/mina.service

# Read the ExecStart command carefully and change its variables according to your own settings and paths.
# Change YOURIP.

[Unit]
Description=Mina Protocol
After=network.target

[Service]
User=root
EnvironmentFile=/root/.mina-env
ExecStart=/usr/local/bin/mina daemon --log-json --log-snark-work-gossip true --internal-tracing --insecure-rest-server --log-level Debug --file-log-level Debug --config-directory /root/.mina-config/ --external-ip YOURIP --itn-keys  f1F38+W3zLcc45fGZcAf9gsZ7o9Rh3ckqZQw6yOJiS4=,6GmWmMYv5oPwQd2xr6YArmU1YXYCAxQAxKH7aYnBdrk=,ZJDkF9EZlhcAU1jyvP3m9GbkhfYa0yPV+UdAqSamr1Q=,NW2Vis7S5G1B9g2l9cKh3shy9qkI1lvhid38763vZDU=,Cg/8l+JleVH8yNwXkoLawbfLHD93Do4KbttyBS7m9hQ= --itn-graphql-port 3089 --uptime-submitter-key  /root/keys/my-wallet --uptime-url https://block-producers-uptime-itn.minaprotocol.tools/v1/submit --metrics-port 10001 --enable-peer-exchange  true --libp2p-keypair /root/keys/libkey --log-precomputed-blocks true --max-connections 200 --peer-list-url  https://storage.googleapis.com/seed-lists/testworld-2-0_seeds.txt --generate-genesis-proof  true --block-producer-key /root/keys/my-wallet --node-status-url https://nodestats-itn.minaprotocol.tools/submit/stats  --node-error-url https://nodestats-itn.minaprotocol.tools/submit/stats  --file-log-rotations 500
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target

```

```console
# Reload daemon and enable mina.service file. Start it and observe logs.

sudo systemctl daemon-reload
sudo systemctl enable mina
sudo systemctl start mina
sudo journalctl -u mina -n 1000 -f

# After a few minutes the node will start working.
# Get the latest status with;

mina client status
```

## BERKELEY TESTNET RELEASE 2.0.0RAMPUP6 (ITN RC2)

```console
# These update steps apply if you have installed Mina Block Producer node with this guide. You can refer to discord channel for Docker steps.
# Stop mina.service, it can take 1-2 minute to stop service, please wait.

systemctl stop mina

# Then, set up and update the rampup Debian Repository for your platform.

sudo rm /etc/apt/sources.list.d/mina*.list

# auto read system config using $(lsb_release -cs)
echo "deb [trusted=yes] http://packages.o1test.net $(lsb_release -cs) rampup" | sudo tee /etc/apt/sources.list.d/mina.list
sudo apt-get update

# Then, update the mina-berkeley to rampup6 from rampup5

sudo apt-get install -y mina-berkeley=2.0.0rampup6-4061884

# Reload daemon and start mina service again.

sudo systemctl daemon-reload
sudo systemctl start mina

# Check latest status of mina.

mina client status
```

The expected output of the mina client status command is:
![image](https://github.com/neuweltgeld/mina-testworld-2/assets/101174090/4a55aa4c-f2dd-44a7-8449-bb190f69a95f)

