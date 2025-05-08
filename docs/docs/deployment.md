---
title: Deploy Python Projects and Scripts with GitHub Actions to DigitalOcean
description: How to set up continuous deployment for your Python projects, scripts, workflows and apps using GitHub Actions and DigitalOcean. One-time setup for push-to-deploy simplicity.
author: Mev-Rael
---

# Deploying Python Projects and Scripts with GitHub Actions to DigitalOcean

In this guide, you'll learn how to set up automatic deployment for your Python app using GitHub Actions and DigitalOcean. This is a one-time setup: once it’s done, you can deploy updates just by pushing code to your repository with `git push`.


## Create a GitHub Repository

If you don’t already have a GitHub repository:

1. Go to [GitHub](https://github.com) and click the **+ New repository** button in the top-left corner.
2. Choose your personal account or organization.
3. Name your repository (e.g. `arkalos-app`).
4. Set it to **Private**.
5. Skip other options and click **Create repository**.
6. Push your local project to this repository:

```bash
# If you haven’t initialized Git commits yet:
git add .
git commit -m "init"

# Set main branch and push to GitHub
git branch -M main
git remote add origin git@github.com:your-username/your-repo.git # copy link from GitHub
git push -u origin main
```

Refresh your GitHub repository page — you should see your project files.



## Set Up a DigitalOcean Droplet

1. Sign up at [DigitalOcean](https://digitalocean.com) and create a **Team** and **Project**.
2. Rename the project in the sidebar to something like `arkalos-app`.
3. Click **Spin up a Droplet**.
4. Choose a region close to you.
5. Under **Choose an image**, select Ubuntu 24.04 (LTS) x64.
6. Choose the **Basic (CPU Options: Regular)** plan. Only $4/month. Or you may choose the cheapest Premium Intel option for $8/month with more power and space. If you plan to have a larger application and use AI, you will have to pick a larger option.
7. Skip other options and under **Authentication Method**, select **SSH Key** and click **Add SSH Key**.
8. If you don’t have an SSH key:
    - Run `ssh-keygen` in your terminal (on VS Code/WSL).
    - Copy the contents of `~/.ssh/id_rsa.pub` into the box.
    - Name it something like "Your Laptop SSH Key".
9. Check the **Add improved metrics monitoring and alerting (free)**
10. Under the "Advanced Options" select **Enable IPv6 (free)**.
10. Set a hostname (e.g. `arkalos-app` or with your domain such as `dashboard.arkalos.com`).
11. Keep quantity 1 and click **Create Droplet**. Once the droplet is ready, you’ll see a green dot indicating it’s active.



## Add a Domain (Optional)

If you want to use a domain instead of the IP address:

### Add a Subdomain
1. Go to your domain provider and open DNS settings of your domain.
2. Add a new **A Record**:
    - Name: e.g., `dashboard` (let say for arkalos.com domain, this will be dashboard.arkalos.com)
    - Value: your droplet’s IP address
3. Add another A record:
    - Name: `www.dashboard`
    - Value: same IP address

### Point a Full Domain to DigitalOcean
1. Or instead, in DigitalOcean, go to **Networking > Domains**.
2. Add your domain (e.g., `arkalos.com`).
3. Create A records to point to the droplet:
    - `@` → droplet IP
    - `www` → droplet IP
4. At your domain registrar, change nameservers to:

```
ns1.digitalocean.com
ns2.digitalocean.com
ns3.digitalocean.com
```

> [!NOTE] 
> If you change nameservers and had DNS settings previously, be sure to reconfigure any email or website or other settings in DigitalOcean.



## Create a New User


### Connect to Droplet via SSH

```bash
ssh root@your-droplet-ip-or-domain
```

Approve the connection if prompted. Then update the system:

```bash
apt update && apt upgrade -y
```

Choose to override config files with the maintainer's version if prompted.

### Create a New User

```bash
adduser arkalos-app # specify a desired username, e.g. arkalos-app
usermod -aG sudo arkalos-app # add a user to sudo group
```

Switch to the new user:

```bash
su - arkalos-app
exit  # Go back to root
exit  # End SSH session
```


## Set Up SSH Access & GitHub Secrets

### On Your Local Machine

```bash
# provide a comment in "", i.e. for what purpose is this key, and a file name at the end
ssh-keygen -t rsa -b 4096 -C "do__arkalos-app__your@email.com" -f ~/.ssh/do_arkalos_app_key
```

Copy the public key:

```bash
cat ~/.ssh/do_arkalos_app_key.pub
```

### On Your Droplet

```bash
ssh root@your-droplet
su - arkalos-app
mkdir -p ~/.ssh && chmod 700 ~/.ssh
touch ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys
nano ~/.ssh/authorized_keys
```

```bash title="Droplet (New user): ~/.ssh/authorized_keys"
# <Your name> & GitHub action's key for DigitalOcean
paste SSH public key here (right mouse click to paste)
```

Paste key into authorized_keys using nano editor. Save and exit with `Ctrl+X`, `Y`, `Enter`.

Then update SSH config:

```bash
sudo nano /etc/ssh/sshd_config

# Ensure these lines are uncommented:
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys .ssh/authorized_keys2

# Save with Ctrl+X, Y, Enter and restart the SSH service
sudo systemctl restart ssh
```


### Add GitHub Repository Secrets

Go to **Settings > Secrets and Variables > Actions** and add:

- `DEPLOY_HOST`: your droplet IP or domain
- `DEPLOY_USER`: arkalos-app or any Ubuntu username you specified
- `DEPLOY_SSH_PRIVATE_KEY`: the content of your private key. Copy the output of `cat ~/.ssh/do_arkalos_app_key`
- `DEPLOY_APP_DIR`: e.g., `/home/arkalos-app/apps/arkalos-app`



### Edit Local SSH Config

```bash title="Your Computer, not VPS"
nano ~/.ssh/config
```

```bash
Host arkalos-app
    HostName your-droplet-ip-or-domain
    User arkalos-app
    IdentityFile ~/.ssh/do_arkalos_app_key
```

Now test with:

```bash
ssh arkalos-app
```

If it works, disable root login.

### Disable Root Login

```bash
sudo nano /etc/ssh/sshd_config

# Change to no:
PermitRootLogin no

# Save the file with Ctrl+X, Y, Enter and Restart the SSH
sudo systemctl restart ssh
```

Try logging in with root again — you should be denied.


## Add a Deploy Key for the Server

On the **Droplet**, generate a key:

```bash title="On the DigitalOcean:"
ssh-keygen -t ed25519 -f ~/.ssh/github_deploy_key -C "deploy-key-arkalos-app"
```

Copy the public key:

```bash
cat ~/.ssh/github_deploy_key.pub
```

On GitHub, go to **Settings > Deploy Keys**, click **Add Key**, and paste it. Do **not** enable write access.


### Update Server's SSH Config

```bash title="On the DigitalOcean:"
nano ~/.ssh/config
```

```bash
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/github_deploy_key
    IdentitiesOnly yes
```

Test the connection:

```bash
ssh -T git@github.com
```



## Set Up Project & Edit .env

Create an application directory and clone the repository.

```bash title="On the DigitalOcean:"
mkdir -p ~/apps/arkalos-app
cd ~/apps/arkalos-app

# copy the link from GitHub, and make sure there is . at the end
git clone git@github.com:your-username/your-repo.git . 

cp .env.example .env

uv sync

cd frontend

npm install

npm run build

cd ..
```

Update `.env` with production credentials.





## Install uv, NodeJS and PM2

Now, add final packages to the server.

**Install uv:**

```bash title="On the DigitalOcean:"
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Now you can check if uv is available by typing `uv -V`.

**Install NodeJS and npm:**

```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -

sudo apt-get install -y nodejs
sudo npm install -g npm@latest
```

Test with `node -v`; you shall see at least v22.14.0.

**Lastly, install PM2:**

PM2 is a simple, yet advanced, production process manager, that allows running scripts and apps on the background at all times and auto restart them.

```bash
sudo npm install pm2 -g

pm2 startup
# this will output the command,
# Copy and run it, it may look something like:
sudo env PATH=$PATH:/usr/bin /usr/lib/node_modules/pm2/bin/pm2 startup systemd -u arkalos-app --hp /home/arkalos-app
```

If you wish to test pm2 locally first on your computer and you are using a WSL, you may need to wrap the PATH= command in quotes, e.g. sudo env "PATH=$PATH:/usr/bin" ...

**Test PM2 is running:**

Check status with `pm2 status`.

Check the details of the process with `pm2 info arkalos-app`

Check the logs with `pm2 logs` or inside the new user's home direcotry ~/.pm2/logs.

If it looks good, save the process and enable it on startup, for example, when server is restarting, with `pm2 save`.


## Configure Nginx & SSL

```bash title="On the DigitalOcean:"
sudo apt install nginx certbot python3-certbot-nginx -y
```

**Create a new website Nginx config file**. If using a domain, Arkalos includes pre-configured files in `.devops/nginx/sites-available/` that you could copy.

```bash title="Create a new site config file:"
# sudo nano /etc/nginx/sites-available/<site name>, i.e.:
sudo nano /etc/nginx/sites-available/arkalos_app.conf

# or with a custom domain, specify your own domain
sudo nano /etc/nginx/sites-available/dashboard.arkalos.com.conf
```

Example config for IP-only:

If you have a domain, check the config example below.

```ini title="Without domain: /etc/nginx/sites-available/example.com.conf"
server {
    listen 80;
    server_name YOUR_DROPLET_IP;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Then:

```bash
# Create a symbolic link to enable the website config
sudo ln -s /etc/nginx/sites-available/example.com.conf /etc/nginx/sites-enabled/

# Disable the default site
sudo rm /etc/nginx/sites-enabled/default

# Test config
sudo nginx -t

# If it's good, reload the service
sudo service nginx reload
```

### For SSL, HTTPS and HTTP2:

This will require a domain.

Generate a new SSL certificate for your domain. SPecify your domain with and without www:

```bash title="DigitalOcean:"
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

Provide your email when asked.

Answer Y to the terms.

Answer Y or N if you wish to receive email updates.

Then you shall see "successfully deployed certificate" and your example.com nginx config file will be updated.

To verify auto-renewal is enabled:

```bash
sudo systemctl status certbot.timer
```


### Nginx site confing for SSL:

> [!NOTE]
> If using domains and SSL, Arkalos projects come with the `.devops` folder and a pre-configured site example file.

1. Rename the `.devops/nginx/sites-available/example.com.conf` file to match the desired file name with your domain or app name.
2. Edit the file and replace `example.com` with your domain everywhere. In the VS Code, press Ctrl+F, search for example.com, then press the ">" arrow on the left and a new line will appear, type your domain into replace box, and click the last Replace All button to the right.
3. The file looks as such:
```ini title="/etc/nginx/sites-available/example.com.conf (With SSL, HTTP2 & Redirects)"
# /etc/nginx/sites-available/example.com.conf

# Replace example.com everywhere with your domain

# Redirect all HTTP traffic to HTTPS
server {
    listen 80;
    listen [::]:80;
    server_name example.com www.example.com;
    return 301 https://example.com$request_uri;
}

# Redirect www to non-www over HTTPS
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name www.example.com;
    return 301 https://example.com$request_uri;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}

# Main server block for non-www
server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
4. After you updated the file in your project, make sure to git push, and then you can simply copy this file on your server. From your app directory:
```bash
cd ~/apps/arkalos-app
sudo cp .devops/nginx/sites-available/domain.com.conf /etc/nginx/sites-available/domain.com.conf
```
5. Make sure there is a symbolic link:
```bash
sudo ln -s /etc/nginx/sites-available/domain.com.conf /etc/nginx/sites-enabled/
```

## Final notes

Arkalos Python projects include pre-configured configs:

- `.github/workflows/deploy.yml`: Automatically runs GitHub deployment action on push to main.
- `.devops/nginx/...`: Ready-to-use Nginx configs.
- `ecosystem.config.js`: PM2 config. Update `name` if needed, and reflect it in `deploy.yml`.

Once this setup is complete, you can deploy by simply pushing to git.
