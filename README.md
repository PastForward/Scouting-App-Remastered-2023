# Scouting-App-Remastered-2022
The new and improved scouting app with complete documentation and instructions (hopefully)
The scouting app utilises HTML, CSS, Apache, and MySql. It is very important that all of these are installed, where applicable. It is very IMPORTANT to note that these instructions are intended for Linux. 

### **Section 1 - Installing Necessary software** 
- You must install the appropriate version fo Apache, this can be done in 2 ways
    PLEASE note that downloading things may NOT work on school wifi because of Smoothwall, you may have to use either a VPN or a phone hotspot.

    1.1 - Install apache using `sudo apt install apache2`

    1.1.2 - Download Apache directly from [here](https://httpd.apache.org/download.cgi), note that the original version used is 2.4.52, newer versions may not be supported.

    1.2 - Using `sudo apt install mysql-server`, this may install the most recent version, which may deprecate the code

    1.3 - Check and confirm that MySql is running by entering `sudo systemctl status mysql`. It should say that it is running.

- Finally please go to test if these are installed correctly and to install further applications (like the connector python) visit the [setup-README.md](https://github.com/FRC-3637-Daleks/Scouting-App-Remastered-2022/blob/14f0c79bc9d863b82ffda5790914cf5350550fc0/db/setup-README.md) located in the `db/` folder. 

### **Section 2 - Double Check That All Dependencies Are Installed**
-  
    2.1  - Go to [here](https://github.com/FRC-3637-Daleks/Scouting-App-Remastered-2022/blob/14f0c79bc9d863b82ffda5790914cf5350550fc0/db/setup-README.md)

    2.2 - Triple check all databases are present 

### **Section 3 - Understanding Databases With Examples**
- 
    3.1 - Databases use tables and other items to well, store data. In the original version of the scouting app we used a 2d table, to store information utilising rows and columns. The columns would contain the data types and the rows would contain said data. Essentially a whole row would be allocated to a team, making it much simpler than a 3d table. For more information, please click [here](https://docs.google.com/spreadsheets/d/1lCQqQTsaWcqok09gHu-j7aPdQEGYPZY-G7KIvSE3v24/edit?usp=sharing). For more information, go to [here](https://github.com/FRC-3637-Daleks/Scouting-App-Remastered-2022/blob/14f0c79bc9d863b82ffda5790914cf5350550fc0/db/setup-README.md) and [here](https://dev.mysql.com/doc/connector-python/en/connector-python-example-ddl.html)
    
    3.2 - Understanding connector python is very important, please visit [here](https://dev.mysql.com/doc/connector-python/en/connector-python-example-cursor-transaction.html), and [here](https://github.com/FRC-3637-Daleks/Scouting-App-Remastered-2022/blob/14f0c79bc9d863b82ffda5790914cf5350550fc0/db/setup-README.md)

    3.3 - Finally, and arguably the most important information you need to know is a basic knowledge of Python and HTML. Using the Python documentation with MySql will be sufficent. The offial MySql with Python documentation can be found [here](https://dev.mysql.com/doc/connector-python/en/connector-python-examples.html). Additionally a "crash course" concerning html and css can be found [here](https://youtube.com/playlist?list=PLblA84xge2_y8F1K0wzPia9V_ULVcfg4k)

### **Section 4 - Understanding and Setting Up CGI**
- 
    4.1 - CGI, also known as the Common Gateway Interface Port, alllows a server to execute external programs. This allows apache2 to pull information from a submitted form using python. This is espcially helpful when pulling information from multiple forms, which need to be submitted into a database. To install CGI, read further.
    
    4.2 - Setting Up CGI. To set up CGI multiple changes must be made to apache2, as apache2 already includes comatability for CGI that isn't turned on. 
    This activates the cgi.load which will enable apache2 to start looking for the the submitted form. 
    ```
    sudo ln -s /etc/apache2/mods-available/cgi.load /etc/apache2/mods-enabled/
    ```
    Next update the contents of `serve-cgi-bin.conf` located in `/etc/apache2/conf-enabled` to match what's below.
    ```
    <IfModule mod_alias.c>
        <IfModule mod_cgi.c>
                Define ENABLE_USR_LIB_CGI_BIN
        </IfModule>

        <IfModule mod_cgid.c>
                Define ENABLE_USR_LIB_CGI_BIN
        </IfModule>

        <IfDefine ENABLE_USR_LIB_CGI_BIN>
                ScriptAlias "/cgi-bin/" "/usr/lib/cgi-bin/"
                <Directory "/usr/lib/cgi-bin">
                        AllowOverride None
                        Options +ExecCGI -MultiViews +SymLinksIfOwnerMatch
                        AddHandler cgi-script .cgi .py
                        Require all granted
                </Directory>
        </IfDefine>
    </IfModule>
    ```
    Finally restart apache 2
    ```
    sudo systemctl restart apache2
    ```
### **Section 5 - Setting Up Forms**
- 
    5.1 Information taken in from items such as radio buttons are usally added as inputs which then python handles, and sequentially sends to your `database.py`. For some additonal help and information vist here[https://www.tutorialspoint.com/passing-radio-button-data-to-cgi-program-in-python].

### **Section 6 - Flask**

    6.1  - Flask is a micro web framework written in Python. To install Flask, you first need to install pip, a package manager for Python. To aquire pip, type
    ```
    sudo apt install python3-pip
    ```
    in the terminal.
    Once the download completes, install Flask through pip by typing
    ```
    sudo pip install Flask
    ```
    to install Flask via pip. Also install WSGI which is the Web Server Gateway Interface and does exactly what you expect it to do. To install it, type the following into the terminal
    ```
    sudo apt-get install libapache2-mod-wsgi-py3
    ```
    Then enable it with `sudo a2enmod wsgi`

    6.2 - Moving files to the right place. To get everything to work, some files need to move to certain locations. The first thing you want to do is remove the `index.html` file within the `/var/www/html` file. Before you do, go to a browser and type `127.0.0.1` and see if the generic ubuntu thing comes up, it should. Then remove the html file within `/var/www/html` by typing 
    
    ```
    sudo rm /var/www/html/index.html
    ```

    Next you want to copy over the flaskapp folder in the Scouting App Remastered folder to the root directory. If you aren't there already, you can get to the root directory by typing `cd ~` in the terminal, then copy the flaskapp folder by typing
    ```
    sudo cp -r Scouting-App-Remastered-2022/flaskapp ./
    ```
 
    after that, copy the other two critical files located in the scouting app folder called `web/pages/static` and `web/pages/templates` into the flaskapp foder in the root directory by typing
    ```
    sudo cp -r Scouting-App-Remastered-2022/web/pages/static flaskapp/
    sudo cp -r Scouting-App-Remastered-2022/web/pages/templates flaskapp/
    ```

    6.3 - config files. The first config file you will want to change would be `/etc/apache2/sites-enabled/000-default.conf` by copying the CONTENTS INSIDE the `000-default.conf` in the `copies` folder into the `000-default.conf` in `sites-enabled`.

    6.4 - moving the folder. After creating the full folder, move it all to its proper place
    ```
    sudo mv flaskapp/ /var/www/html/
    ```

    This will replace that file with the custom made one that gets Flask to work.

    6.4 - permissions. Make sure that the others have read and execute permissions are given to the flaskapp folder and everything in it with this command
    ```
    sudo chmod o+rx -R /var/www/html/flaskapp/
    ```
    make sure that the `/var/www/html/flaskapp/` are ALL given their respective permissions with the following commands
    ```
    sudo chmod o+rx -R /var/www/html/
    ```

    6.5 - flask-mysql. Lastly, install flask-mysql by typing this within the console:
    ```
    sudo pip install flask-mysql
    ```
    for good measure, restart apache as well with a good ol' `sudo systemctl restart apache2` and see if it runs by typing `127.0.0.1` into your internet browser of choice!