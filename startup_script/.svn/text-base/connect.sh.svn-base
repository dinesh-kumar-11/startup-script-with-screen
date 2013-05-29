#!/bin/bash

#	-------------------------------------------------------------------
#
#	Startup Shell program to startup the scripts automatically
#
#	Copyright 2013, Dinesh sarangapani <dinesh.sarangapani@aspiresys.com, msg2dineshkumar@gmail.com>.
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
#	-------------------------------------------------------------------

#	-------------------------------------------------------------------
#	Constants
#	-------------------------------------------------------------------
PROGNAME=$(basename $0)
VERSION="0.0.1"
USAGE="Usage: `basename $0` [-hv] [-u arg] [-d arg] [-f arg] [-l arg] [-x arg] [-s arg]"


function start_window {
#	-----------------------------------------------------------------------
#	Function to Start the window inside a screen based on the name
#		one argument the file name acting as the screen session name
#       two argument the window number, if existing window given the next is taken
#       three argument the name of the window
#       four argument the command to execute in the window created
#	-----------------------------------------------------------------------
echo "Trying to start ${3}...."
screen -X -S ${1} screen -t ${3} ${2}
screen -X -S ${1} -p ${2} stuff "${4}
"
echo "'${3}' started successfully...."  | colorize GREEN
}



function start_screen {
#	-----------------------------------------------------------------------
#	Function to Start the screen based on the name
#		one argument the file name acting as the screen session name
#	-----------------------------------------------------------------------
  screen -d -m -S ${1}
  echo "--------------------------------------------------------------------------"
  echo "Screen Session Started for ${1}"  | colorize GREEN
  echo "--------------------------------------------------------------------------"
}



function read_config {
#	-----------------------------------------------------------------------
#	Function to read the config file one by one and
#   Identify the script line by "=" and create a screen window
#		one argument the file name
#	-----------------------------------------------------------------------
  i=1
  start_screen ${1}
  conffile=$configpath${1}
  while read line; do
    if [[ "$line" =~ "=" ]]; then
      name[i]=${line%% =*}
      value[i]=${line##*= }
      start_window ${1} $i ${name[i]} "${value[i]}"
      ((i++))
    fi
  done < "$conffile"
}



function graceful_exit
{

#	-----------------------------------------------------------------------
#	Function called for a graceful exit, add anything up here
#		No arguments
#	-----------------------------------------------------------------------
	exit
}


function stop_connect 
{
#	-----------------------------------------------------------------------
#	Function called for a Stopping the Process
#		One argument on which for which instance the screen has to stop
#	-----------------------------------------------------------------------
   if [ ! -z "$1" ]; then
     echo "Killing screen for ${1}" | colorize GREEN
     screen -S "${1}" -X quit
     graceful_exit
   fi
   for item in ${configfiles[*]}
   do
     echo "--------------------------------------------------------------------------"
     screen -S "${item}" -X quit 
     echo "Killing screen for for ${item}" | colorize GREEN
     echo "--------------------------------------------------------------------------"
   done
}

function startup_connect 
{
#	-----------------------------------------------------------------------
#	Function called for a Starting the screen and all process in a 
#   seperate window
#		One argument on which for which instance the screen has to stop
#	-----------------------------------------------------------------------
    if [ ! -z "$1" ]; then
     read_config "$1"
     graceful_exit
    fi
	for item in ${configfiles[*]}
	do
	  read_config "${item}"
	done
}

function usage
{

#	-----------------------------------------------------------------------
#	Function to display usage message (does not exit)
#		No arguments
#	-----------------------------------------------------------------------

	echo "Usage: ${PROGNAME} [-h | --help] [-c configuration path] [start {} |stop {}]" | colorize YELLOW
}


function helptext
{
#	-----------------------------------------------------------------------
#	Function to display help text (does not exit)
#		No arguments
#	-----------------------------------------------------------------------
   echo "This file as the following usage"
   usage
   echo "Example: ./${PROGNAME} -c /var/www/connect/configs/ start" | colorize BLUE
   echo "To start a specific session instance specify the config file name, below 'theday' is a config file name in the path"
   echo "Example: ./${PROGNAME} -c /var/www/connect/configs/ start theday" | colorize BLUE
   echo "The same is applicable to kill the screen and stop all the scripts"
}

function version 
{
#	-----------------------------------------------------------------------
#	Function to display help text (does not exit)
#		No arguments
#	-----------------------------------------------------------------------
	echo "${PROGNAME} ver. ${VERSION}"
}

colorize(){
#	-----------------------------------------------------------------------
#	Function to color the display text
#		No arguments
#	-----------------------------------------------------------------------
    GREEN="\033[0;32m"
    CYAN="\033[0;36m"
    GRAY="\033[0;37m"
    BLUE="\033[0;34m"
    YELLOW="\033[0;33m"
    RED='\e[0;31m' # Red
    NORMAL="\033[m"
    color=\$${1:-NORMAL}
    # activate color passed as argument
    echo -ne "`eval echo ${color}`"
    # read stdin (pipe) and print from it:
    cat
    # Note: if instead of reading from the pipe, you wanted to print
    # the additional parameters of the function, you could do:
    # shift; echo $*
    # back to normal (no color)
    echo -ne "${NORMAL}"
}



#	-------------------------------------------------------------------
#	Program starts here
#	-------------------------------------------------------------------
#
#	-------------------------------------------------------------------
#	Initialization And Setup
#	-------------------------------------------------------------------


# Set file creation mask so that all files are created with 600 permissions.

umask 066


# Trap TERM, HUP, and INT signals and properly exit

trap "signal_exit TERM" TERM HUP
trap "signal_exit INT"  INT


##### Command Line Processing #####

if [ "$1" = "--help" ]; then
	helptext
	graceful_exit
fi

if [ "$1" = "--version" ]; then
	version
	graceful_exit
fi

while getopts ":c:" opt; do
	case $opt in
		c )	configpath=$OPTARG;;
		* )	helptext
			exit 1
	esac
done

if [ -z "$3" ]; then
	helptext
	graceful_exit
fi

if [ -z "$configpath" ]; then
	helptext
	graceful_exit
fi

configfiles=`find  "$configpath"* | xargs -i basename {}`

if [ -z "$configfiles" ]; then
	helptext
	echo "There are no configuration files in the specified path $configpath" | colorize RED
	graceful_exit
fi



if [ "$3" = "start" ]; then
	startup_connect $4
	graceful_exit
fi

if [ "$3" = "stop" ]; then
	stop_connect $4
	graceful_exit
fi
