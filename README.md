occi-os
=======

This is a clone of https://github.com/dizz/nova - it provides a python egg which can be deployed in OpenStack and will thereby add the 3rd party OCCI interface to OpenStack.

Usage
-----

1. Install this egg: python setup.py install (later maybe pip install occi-os)
2. Configure OpenStack - Add application to api-paste of nova and enable the API

### Configuration

Make sure an application is configured in api-paste.ini (name can be picked yourself):

	########
	# OCCI #
	########

	[composite:occiapi]
	use = egg:Paste#urlmap
	/: occiapppipe

	[pipeline:occiapppipe]
	pipeline = authtoken keystonecontext occiapp
	# with request body size limiting and rate limiting
	# pipeline = sizelimit authtoken keystonecontext ratelimit occiapp

	[app:occiapp]
	use = egg:occi-os#occi_app

Make sure the API (name from above) is enabled in nova.conf:

	[...]
	enabled_apis=ec2,occiapi,osapi_compute,osapi_volume,metadata
	[...]
	
#### Hacking the port number

Currently nova compute will start the app you defined on an random port. If you want to have it on a predefined code you will need to edit the 'flags.py' file <path to nova>/nova/:

    cfg.IntOpt('occiapi_listen_port',
               default=8989,
               help='the port for external test apps'),

Again the name you provide here should be identical to the name of the app you provided above!
			   
### For development

Make sure the nova compute api is in the path for Python and if you wanna test the app run:

	paster serve api-paste.ini --reload
