## Install Odoo on Ubuntu 18.04.
0. Be sure to have `main restricted universe multiverse` in your `/etc/apt/sources.list`;
1. `sudo apt update && sudo apt upgrade`;
2. `sudo apt install git python3-pip build-essential wget python3-dev python3-venv python3-wheel libxslt-dev libzip-dev libldap2-dev libsasl2-dev python3-setuptools node-less`;
3. `sudo useradd -m -d /opt/odoo -U -r -s /bin/bash odoo`;
4. `sudo apt-get install postgresql`;
5. `sudo su - postgres -c "createuser -S odoo"` (as stated in [Odoos's Official Documentation](http://www.odoo.com/documentation/10.0/setup/deploy.html#postgresql), the postgresql user must _not_ be a a super-user);
6. `cd /tmp && sudo wget https://builds.wkhtmltopdf.org/0.12.1.3/wkhtmltox_0.12.1.3-1~bionic_amd64.deb`;
7. `sudo dpkg -i wkhtmltox_0.12.1.3-1~bionic_amd64.deb` (ignore errors);
8. `sudo apt-get install -f`;
9. `sudo ln -s /usr/local/bin/wkhtmltopdf /usr/bin`;
10. `sudo ln -s /usr/local/bin/wkhtmltoimage /usr/bin`;
11. `sudo su - odoo`;
12. `git clone https://www.github.com/odoo/odoo --depth 1 --branch 11.0 /opt/odoo/odoo11`;
13. `cd /opt/odoo && python3 -m venv odoo11-venv`;
14. `source odoo11-venv/bin/activate`;
15. `pip3 install wheel`;
16.  Edit `/opt/odoo/odoo11/requirements.txt`: comment with a `#` all lines starting with `lxml` and add a new line with just `lxml` on it;
16. `pip3 install -r odoo11/requirements.txt`;
17. `deactivate`;
18. `exit`;
19. `sudo mkdir /opt/odoo/odoo11-custom-addons && sudo chown odoo: /opt/odoo/odoo11-custom-addons`;
20. `sudo cp /opt/odoo/odoo11/debian/odoo.conf /etc/odoo11.conf`;
21. Edit `/etc/odoo11.conf` (remember to `sudo`), it should look like this:
    ```
	[options]
	; This is the password that allows database operations:
	admin_passwd = my_admin_passwd
	db_host = False
	db_port = False
	db_user = odoo
	db_password = False
	addons_path = /opt/odoo/odoo11/addons
	; If you are using custom modules
	; addons_path = /opt/odoo/odoo11/addons,/opt/odoo/odoo11-custom-addons
	```
22. `cd /etc/systemd/system && sudo touch odoo11.service`;
23. Edit the created `odoo11.service` file (remember to `sudo`), it should look like this:
    ```
	[Unit]
	Description=Odoo11
	Requires=postgresql.service
	After=network.target postgresql.service

	[Service]
	Type=simple
	SyslogIdentifier=odoo11
	PermissionsStartOnly=true
	User=odoo
	Group=odoo
	ExecStart=/opt/odoo/odoo11-venv/bin/python3 /opt/odoo/odoo11/odoo-bin -c /etc/odoo11.conf
	StandardOutput=journal+console

	[Install]
	WantedBy=multi-user.target
	```
24. `sudo systemctl daemon-reload`;
25. `sudo systemctl start odoo11`;
26. `sudo systemctl status odoo11`
    ```
	outputs:
	● odoo11.service - Odoo11
	Loaded: loaded (/etc/systemd/system/odoo11.service; disabled; vendor preset: enabled)
	Active: active (running) since Thu 2018-05-03 21:23:08 UTC; 3s ago
	Main PID: 18351 (python3)
	 Tasks: 4 (limit: 507)
	CGroup: /system.slice/odoo11.service
			└─18351 /opt/odoo/odoo11-venv/bin/python3 /opt/odoo/odoo11/odoo-bin -c /etc/odoo11.conf
	```
27. `sudo systemctl enable odoo11`;
28. Go to `http://<your_domain_or_IP_address>:8069`;

See https://linuxize.com/post/how-to-deploy-odoo-11-on-ubuntu-18-04/#disqus_thread
