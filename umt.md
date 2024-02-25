## Launching the Nodes
This guide is intended for Mina UMT Block Producers. If you encounter a bug, please get in touch through the #umt-testnet channel in Discord.

```console
# First, set up and update the umt Debian Repository for your platform.
sudo rm /etc/apt/sources.list.d/mina*.list

# auto read system config using $(lsb_release -cs)
sudo echo "deb [trusted=yes] http://packages.o1test.net $(lsb_release -cs) umt" | sudo tee /etc/apt/sources.list.d/mina.list
sudo apt-get update

# Then, install the package or packages that you need:
sudo apt-get install --allow-downgrades -y mina-devnet=1.0.0umt-2025a73
```

```console
# After setting up your nodes you need to add your keys to your node.
# Please use the same keypair in all of your Network Performance Testing nodes.
# Do not share your keypairs with anyone.

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
mina advanced generate-libp2p-keypair -privkey-path /root/keys/libkey

# Set a password. And this password will be yours MINA_LIBP2P_PASS
# MINA_PRIVKEY_PASS can be found in the file community-yyy-password.txt that you extracted.

# Change the following "PASS" variables (do not delete double quotes) as above. 
RAYON_NUM_THREADS=6
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
ExecStart=mina daemon \
--block-producer-key /root/keys/my-wallet \
--config-directory /root/.mina-config/ \
--enable-peer-exchange true \
--external-ip YOURIP \
--file-log-level Debug \
--generate-genesis-proof true \
--insecure-rest-server \
--discovery-keypair /root/keys/libkey \
--log-json \
--log-level Debug \
--log-precomputed-blocks true \
--log-snark-work-gossip true \
--metrics-port 10001 \
--node-error-url https://nodestats-itn.minaprotocol.tools/submit/stats \
--node-status-url https://nodestats-itn.minaprotocol.tools/submit/stats \
--peer-list-url https://storage.googleapis.com/o1labs-gitops-infrastructure/o1labs-umt-pre-fork-run-1/seed-list-o1labs-umt-pre-fork-run-1.txt

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


The expected output of the mina client status command is:
![image](https://github.com/neuweltgeld/mina-testworld-2/assets/101174090/300a8241-e6a5-486a-b5de-be386c74f065)




