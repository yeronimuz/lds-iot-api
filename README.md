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
* When a device is added and registered, it gets the MQTT configuration returned. This mqtt configuration is inherited from the related site to which this device was added
* When registering a sensor or actuator, it will be encapsulated by a device. A MAC address will have to be provided. The sensor will be stored as part of a Device. The Device will be part of a Site.

# Automation
* When configuring scenes or other actions that need to happen on certain system conditions (Predicate), the Command object was created.
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
  * Normal: The device is in its normal operation phase. It can accept and send mqtt messages

# Powermeter device
* The P1 power meter is an example of a sensor device. 
* Logically, the P1 smart meter will consist of a separate sensor for each value type that can be measured.
* It has parameters for serial port configuration for connecting a P1 smart meter reader
* It has a few operational parameters for setting repeating values and a buffer size
  * The repeatValuesAfter determines the amount of seconds to wait before all measured values are sent out again. The power meter reader only sends changed values otherwise.
  * The internalQueueSize When the MQTT connection is not available, measurements are stored internally. Note that when connection is available again, the values are all sent out without delay.

# Sequence of registration
- Start web UI and add a Site with at least the MQTT broker details and unique name configured if not done already
- Register a new Device by starting it. It will register itself using the API it knows by default (yaml file). But it has no notion of any Site it could belong to. So the call will only return when the user has assigned the device to a site.
- When the device is assigned to the site, the REST call will return with the site information filled. When the site was already registered previously, the local last known Site configuration will be replaced by the one returned from the backend. This makes it possible to reconfigure the system.
Reconfiguring a device is done by a power cycle.

# Software updates
The bootprocess of a device will remain alive during normal operation. It will subscribe to the mqtt software update topic. It there is an update available (device manufacturer and modelId match, and the local version is different from the advertised one)

When starting the powermeter device, it only knows the URL of the registration API. 
It will register itself by POST-ing a new Site. This site must contain the power meter device. 
The device will contain the serial configuration and MAC address.
When the registration is finished, the return value will hold the mqtt configuration. With this configuration, an mqtt client can be started.
Once the powermeter device is operational, the measured values are automatically registered as possible parameters in automation conditions.

So what must be configured in the database before anything can start is returning a default mqtt_configuration with the mqtt broker details.
The device may have to be updated in the database with the clientId
The serial port configuration may be very specific to the local configuration. It is therefore better to load the serial configuration from file. 
