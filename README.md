startup-script-with-screen
==========================

It is a start up script which initialize the screen and starts jobs on a different window from the config file

USAGE:


#	-------------------------------------------------------------------
#
#	Startup Shell program to startup the scripts automatically
#
#	Author, Dinesh Kumar <dineshkumarinchennai@gmail.com, msg2dineshkumar@gmail.com>.
#       
#
#	
#	Description:
#         This is a centralized script which invokes the other script inside a session.
#
#
#	Usage:
#
#		connect.sh [-h | --help] [-c configuration path] [start {} |stop {}]
#
#	Options:
#
#		-h, --help	Display this help message and exit.
#		-c  configuration-path the path where the configuration files are created
#		start|stop either start or stop the screen session with the scripts in config file
#		{} config-filename the configuration file name to which the scripts needs to be initiated
#   
#   Configuration file Format:
#      filename : the config file name will be given as the screen name
#      the file should contain the format,
#      window_name = command to execute in the window
#      example:
#            connect_cron = sh /var/www/greenland1.1/scripts/transformers/connect_cron.sh dev1.greenland.com 
#
#	Revision History:
#    ver.0.0.1  - Dinesh Kumar (dinesh.sarangapani)
#	
#
