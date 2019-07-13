# FSND-Linux_configuration
Linux box configuration.

## Overview
This is the fifth project for Udacity Full Stack Development. The main purpose of this is to configure the linux box so that we can host the web application. This will walk you through how to configure you linux box in order to host a Flask app.

## Requirements
```
* AWS developer account
* Lighsail instance
```

## How to
### Step one
Navigate to the following link: https://console.aws.amazon.com and set up your account.  
We will be using the lightsail instance. Click on "Build Using Virutal Service - with Light Sail."  
Click on "linux", "OS Only", and select your version of Ubuntu (I have chosen - 16.x).
Once your instance is set up and running, connnect to your VM. This will open a window and you now have a terminal 
for you to interact with.  

You will now configure a port for the VM so that it is open.  
Navigate to the lightsail instance on console.aws page. - click on the three dots and click on "manage."
Navigate to the "Network" section and click on "Add" Type the following in.
```
custom | port: 2200
```

### Step Two
Here we are going to configure the firewall and add a user.  
Make sure that the firewall is disable before we start.  
