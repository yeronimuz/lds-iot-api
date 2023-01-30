# Domiot API
This project is an API for the Lankheet Domotiot domotics System.
## Description of the data
* **Site** is the heart of the system
* It has at least an admin **User**
* The admin User must be present in the Site data when adding a new Site
* A user is admin when it has the admin role
* A **Role** has one or more **Permission**
* The User may be related to more than one site. 
* For each site, the user may have a different role
* Site has a configuration
* Configuration consists of at least an MQTT configuration for connection to an MQTT broker
* It may also have a serial configuration for connecting a P1 smart meter reader
* It may also have a powermeter configuration. The powermeter has a few operational parameters for setting repeating values and a buffer size
  * The repeatValuesAfter determines the amount of seconds to wait before all measured values are sent out again. The power meter reader only sends changed values otherwise.
  * The internalQueueSize When the MQTT connection is not available, measurements are stored internally. Note that when connection is available again, the values are all sent out without delay.
* A site has one or more devices
* A **Device** consists of zero or more sensors and/or zero or more actuators
* When registering a sensor, it will be encapsulated by a device. A MAC address will have to be provided. The sensor will be stored as part of a Device. The Device will be part of a Site.
