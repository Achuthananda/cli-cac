#! /usr/bin/env python

""" Copyright 2017 Akamai Technologies, Inc. All Rights Reserved.

 Licensed under the Apache License, Version 2.0 (the "License");
 you may not use this file except in compliance with the License.

 You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

 Unless required by applicable law or agreed to in writing, software
 distributed under the License is distributed on an "AS IS" BASIS,
 WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 See the License for the specific language governing permissions and
 limitations under the License.

 ****************************************************************
 * Image Manager CLI module by Javier Garza (jgarza@akamai.com) *
 * Based on https://github.com/brassic-lint/cli-imgman          *
 *       from Gareth Hughes                                     *
 ****************************************************************

"""
# Libraries commmon to python 2 and 3
from __future__ import print_function
import sys
import os
import logging
import random
import re
import requests
import json
import urllib
import texttable as tt
from future import standard_library
from future.builtins import next
from future.builtins import object
from http_calls import EdgeGridHttpCaller
from akamai.edgegrid import EdgeGridAuth
from config import EdgeGridConfig
from subprocess import call
standard_library.install_aliases()

if sys.version_info[0] >= 3:
     # python3
	from urllib.parse import urljoin
else:
     # python2.7
     from urlparse import urljoin

session = requests.Session()
debug = False
verbose = False
cache = False
format = "json"
section_name = "default"

# If all parameters are set already, use them.  Otherwise
# use the config
config = EdgeGridConfig({"verbose": False}, section_name)

if hasattr(config, "debug") and config.debug:
    debug = True

if hasattr(config, "verbose") and config.verbose:
    verbose = True

if hasattr(config, "cache") and config.cache:
    cache = True


# Set the config options
session.auth = EdgeGridAuth(
    client_token=config.client_token,
    client_secret=config.client_secret,
    access_token=config.access_token
)

if hasattr(config, 'headers'):
    session.headers.update(config.headers)

session.headers.update({'User-Agent': "AkamaiCLI"})

baseurl_prd = '%s://%s/' % ('https', config.host)
prdHttpCaller = EdgeGridHttpCaller(session, debug, verbose, baseurl_prd)

def listConfigs():
    """ List the CAC Configurations on a given network """

    #Temp code
    params = {'accountSwitchKey':'1-X06DH9'}
    configlist = prdHttpCaller.getResult("/client-access-control/v1/configurations",params)
    return(configlist)


def getConfig(config_id):
    """ Gets a specific policy on a given network in JSON format """

    print("Retrieving: " + config_id )
    #Temp code
    params = {'accountSwitchKey':'1-X06DH9'}
    configdetail = prdHttpCaller.getResult("/client-access-control/v1/configurations/"
                                               + config_id,params)
    return(configdetail)

def acknowledgeCidr(config_id,version_id):
    """ Acknowledges CIDR for a specific version of CAC Config"""
    data = {}
    #Temp code
    #params = {'accountSwitchKey':accountSwitchKey}
    ackdetail = prdHttpCaller.putResult("/client-access-control/v1/configurations/"
                                               + config_id+"/acknowledge/"+version_id,data,params)
    return(ackdetail)


def formatOutputConfigList(configlist, output_type):
    """ Formats the output on a given format (json or text) """
    if output_type == "json":
        # Let's print the JSON
        print(json.dumps(configlist, indent=2))

    if output_type == "text":
        # Iterate over the dictionary and print the selected information
        ParentTable = tt.Texttable()
        ParentTable.set_cols_width([30,25,35])
        ParentTable.set_cols_align(['c','c','c'])
        ParentTable.set_cols_valign(['m','m','m'])
        Parentheader = ['Config Name','Config Id','Description']
        ParentTable.header(Parentheader)
        for my_item in configlist:
            Parentrow = [ my_item["name"],my_item["configurationId"],my_item["description"]]
            ParentTable.add_row(Parentrow)
        MainParentTable = ParentTable.draw()
        print(MainParentTable)

def formatOutputConfig(config, output_type):
    """ Formats the output on a given format (json or text) """
    if output_type == "json":
        # Let's print the JSON
        print(json.dumps(config, indent=2))

    if output_type == "text":
        # Iterate over the dictionary and print the selected information
        ParentTable = tt.Texttable()
        ParentTable.set_cols_width([30,25,35,35])
        ParentTable.set_cols_align(['c','c','c','c'])
        ParentTable.set_cols_valign(['m','m','m','m'])
        Parentheader = ['Config Name','Config Id','Current CIDRs','Proposed CIDRs']
        ParentTable.header(Parentheader)
        if len(config["proposedCidrs"]) == 0:
            temp = {}
            Parentrow = [ config["name"],config["configurationId"],config["currentCidrs"]["cidrs"], temp]
        else:
            Parentrow = [ config["name"],config["configurationId"],config["currentCidrs"]["cidrs"], config["proposedCidrs"][0]["cidrs"]]
        ParentTable.add_row(Parentrow)
        MainParentTable = ParentTable.draw()
        print(MainParentTable)


def main():
    """ Processes the right command (list-configs, get-configs, acknowledge-cidrs) """
    if config.command == "list-configurations":
        # Get the list of policies in JSON format for the given network
        configList = listConfigs()
        formatOutputConfigList(configList, config.output_type)

    elif config.command == "get-configuration":
        configDetail = getConfig(config.id)

        if hasattr(config, 'output_file') and config.output_file is not None:
            config.output_file.write(json.dumps(configDetail, indent=2))
            config.output_file.close()
        else:
            formatOutputConfig(configDetail, config.output_type)

    elif config.command == "acknowledge-cidr":
        #Please ask for acknowledgement.
        i = 1
        while i < 2:
            decision = raw_input("Are you sure you want to acknolwedge the cidr [yes/no]:")
            if decision == "yes" or decision == 'y':
                print("Now Acknowleding the CIDR block")
                ackdetail = acknowledgeCidr(config.config_id,config.version_id)
                break
            elif decision == 'no' or decision == 'n':
                print("Aborting the acknowledgement")
                break
            else:
                print("Please enter the Correct Input")

if __name__ == "__main__":
    main()
