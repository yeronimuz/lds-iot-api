# Domiot API
This project is an API for the Lankheet Domiot domotics System.
## Description of the data
* **Site** is the heart of the system
* It has at least an admin **User**
* The admin User must be present in the Site data when adding a new Site
* A user is admin when it has the admin role
* A **Role** has one or more **Permission**
* The User may be related to only one site.
* Site has a configuration
* Configuration consists of an MQTT configuration for connection to an MQTT broker
* A site may consist of zero or more devices
* A device consists of zero or more Sensors and/or zero or more Actuators
* A device has a configuration
* The system maintains configuration templates determined by manufacturer, model, type and software version.
* When registering a sensor or actuator, it will be encapsulated by a device. A MAC address will have to be provided. The sensor will be stored as part of a Device. The Device will be part of a Site.

# Automation
* For configuring scenes or other actions that need to happen on certain system conditions (Predicate), the Command object was created.
* A device can have zero or more commands. These commands are advertised to the Domiot system. 
* A command can change operational parameters. E.g. 'Set powermeter_device1.repeatValuesAfter 5000'
* A command can operate a switch. E.g. 'Set power_switch1 to ON'
## conditions
* A condition is a variable with a value range. For example humidity_sensor1.humidity_level <= 60
* Variable name is a reference to the device.sensor.sensorvalueName
* The equality operator denotes whether it should be tested equals, not_equals, less than, greater than, less than or equals, greater than or equals, within a range (boundaries inclusive) or out of range (boundaries exclusive)
* The precision of the values is always taken into account. It is therefore impossible to make rounding mistakes

# Device operational status
* When a device is started, it will work its way through the following phases
  * Booting: Consult the Domiot system for new Device software. If there is new software, start the update process. See ...
  * Registering: If there is no device update available, the Device registers itself by consulting the registration API, see **Sequence of registration**
  * Normal: The device is in its normal operation phase. It can accept and send mqtt messages. It may act upon configuration changes or software updates.

# Sequence of registration
- Start web UI and add a Site with at least the MQTT broker details and unique name configured if not done already
- Register a new Device by starting it. It will register itself using the standard registration MQTT topic. The sent message will have an identification with MAC address and device model and type. This will be picked up by the system and a new device will show up in the web UI.
- The user may add it to a site and configure it by setting parameters. These parameters will be sent to the specific device by means of an MQTT configure message. The relevant parameters will be derived from default configurations in the database. These configurations are manufacturer, model and type specific.

# Software updates
A device may receive a request to update it's software. Depending on the value of the parameter "software_updates" with values true or false (update or not), the device will put itself into update mode.
Note that for software updates, new configuration parameters may be needed, old ones may no longer be used.

# MQTT topics
The Domiot topics are organized as follows:
#siteId/floor/room/device/sensor|actutor/sensor|actuatorId

# Powermeter device
* The P1 power meter is an example of a sensor device. 
* Logically, the P1 smart meter will consist of a separate sensor for each value type that can be measured.
* It has parameters for serial port configuration for connecting a P1 smart meter reader
* It has a few operational parameters:
  * SoftwareUpdates, false: Device does not accept software updates, true: Software updates are automatically installed, default: true
  * Local storage, off: No local storage is done, redis: Use Redis to store measurements locally, file: Use file storage to buffer values. 
    Note that buffering is always done in memory. Redis is fast and file storage is slow.
  * The repeatValuesAfter determines the amount of seconds to wait before all measured values are sent out again. The power meter reader only sends changed values otherwise.
  * The internalQueueSize When the MQTT connection is not available, measurements are stored internally. Note that when connection is available again, the values are all sent out without delay.
