# Linux-Server-Configuration
Setting up an AWS Lightsail Ubuntu Server with a Catalog python app

1. Server is available @ http://52.42.97.248.
2. Login to the server using SHH port 2200 with account Grader.
3. The Grader SSH key is provided speratly to the reviewr.
4. the configuration changes to the server are as follow -
  a. UFW is Enabled.
  b. UFW enabled are 2200, 80, 8000, 123
  c. UFW Status - 
        Status: active
            To                         Action      From
            --                         ------      ----
            22                         DENY        Anywhere
            2200/tcp                   ALLOW       Anywhere
            80/tcp                     ALLOW       Anywhere
            123                        ALLOW       Anywhere
            8000/tcp                   ALLOW       Anywhere
            22 (v6)                    DENY        Anywhere (v6)
            2200/tcp (v6)              ALLOW       Anywhere (v6)
            80/tcp (v6)                ALLOW       Anywhere (v6)
            123 (v6)                   ALLOW       Anywhere (v6)
            8000/tcp (v6)              ALLOW       Anywhere (v6)
  d. SSH is listening on port 2200.
5. Application Catalog is available at the root of of the server at http://52.42.97.248
6. The application is a python flask application and is installed through mod-wsgi using VirtualEnv.
7. installation stracture is -
              /var/www/catalog
              └── catalog
                  ├── static
                  ├── templates
                  └── venv
8. Packages installed - 
  a. mod-wsgi
  b. virtualenv
  c. PIP
  d. Flask
  e. flaks_sqlalchemy
  f. requests
  g. oauth2client
  h. flickrAPI
  i. psycopg2
9. Revised configuration files -
  a. /var/www/catalog/catalog.wsgi -
        #!/usr/bin/python
        activate_this = '/var/www/catalog/catalog/venv/bin/activate_this.py'
        execfile(activate_this, dict(__file__=activate_this))

        import sys
        import logging
        logging.basicConfig(stream=sys.stderr)
        sys.path.insert(0,"/var/www/catalog/catalog/")

        from application import app as application
        application.secret_key = 'My Super Secret Key'
    b. /etc/apache2/sites-available/catalog.conf - 
          <VirtualHost *>
                      ServerName MyWebSite
                      ServerAdmin admin@mywebsite.com
                      WSGIDaemonProcess catalog user=www-data group=www-data threads=5
                      WSGIScriptAlias / /var/www/catalog/catalog.wsgi
                      <Directory /var/www/catalog/catalog/>
                              WSGIProcessGroup catalog
                              WSGIApplicationGroup %{GLOBAL}
                              WSGIScriptReloading On
                              Order allow,deny
                              Allow from all
                      </Directory>
                      Alias /static /var/www/catalog/catalog/static
                      <Directory /var/www/catalog/catalog/static/>
                              Order allow,deny
                              Allow from all
                      </Directory>
                      ErrorLog ${APACHE_LOG_DIR}/error.log
                      LogLevel warn
                      CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>
    c. /etc/hosts - 
          127.0.0.1 localhost ip-52.42.97.248
          127.0.0.1:8000 MyWebSite ip-52.42.97.248:8000

          # The following lines are desirable for IPv6 capable hosts
          ::1 ip6-localhost ip6-loopback
          fe00::0 ip6-localnet
          ff00::0 ip6-mcastprefix
          ff02::1 ip6-allnodes
          ff02::2 ip6-allrouters
          ff02::3 ip6-allhosts

 10. Enjoy it!   
