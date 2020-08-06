# Akamai CLI: Client Access Control Module

This module enables the use of Client Access Control (CAC) in the Akamai CLI tool

## API Permissions

Please ensure your API client has access to the "Client Access Control" API (you may need to create a separate API client)

## Install

To install, use [Akamai CLI](https://github.com/akamai/cli):

```
$akamai install https://github.com/Achuthananda/cli-cac.git
```

You may also use this as a stand-alone command by cloning this repository
and compiling it yourself.

## Usage

```
$akamai cac [global flags] Commands
```

## Global Flags
- `--edgerc value` — Location of the credentials file (default: user's directory like "/Users/apadmana") [$AKAMAI_EDGERC]
- `--section value` — Section of the credentials file (default: "default") [$AKAMAI_EDGERC_SECTION]
- `--debug` - `-d` - prints debug information
- `--verbose` - Print verbose information
- `--version`, `-v` — Print the version
- `--help`, `-h` — Show help

## Commands  
- `list-configurations` —  Lists all Client Access Control (CAC) configurations to which you have access. Output can be formatted as JSON, or text tables (default); which is a human readable ascii table showing the policy name, creation date and creation user (useful for inventorying purposes).
- `get-configuration` — Retrieves a given CAC configuration for a specific configuration id (output can be saved into a file)
- `acknowledge-cidr` - Acknowledge the Proposed CIDRs.

Required arguments:
  --configuration-id <config_id>
  --version <config_version>

## Examples

#### Help
This displays the usage of CAC akamai CLI.
```
$akamai cac --help
usage: akamai-cac [-h] [--verbose] [--debug] [--edgerc credentials_file]
                  [--section credentials_file_section]
                  [--accountSwitchKey Account Switch Key]
                  {list-configurations,get-configuration,acknowledge-cidr} ...

Process command line options.

positional arguments:
  {list-configurations,get-configuration,acknowledge-cidr}
                        commands
    list-configurations
                        List all Configurations
    get-configuration   Gets a specific configuration
    acknowledge-cidr    Acknowledge CIDR

optional arguments:
  -h, --help            show this help message and exit
  --verbose, -v         Verbose mode
  --debug, -d           Debug mode (prints HTTP headers)
  --edgerc credentials_file, -e credentials_file
                        Location of the credentials file (default is
                        ~/.edgerc)
  --section credentials_file_section, -c credentials_file_section
                        Credentials file Section's name to use
  --accountSwitchKey Account Switch Key, -a Account Switch Key
                        Switch key to different account

```
#### Usage of list-configurations Command
This shows how to use list-configurations.
```
$ akamai cac list-configurations -h
          [or]
$ akamai cac list-configurations --help
usage: akamai-cac list-configurations [-h] [--output-type json/text]

optional arguments:
  -h, --help            show this help message and exit
  --output-type json/text, -t json/text
                        Output type {json, text}. Default is text
```


#### List All the CAC Configurations
This shows all the CAC Configurations in the Account.
```
$ akamai cac --section default list-configurations
+--------------------------------+---------------------------+-------------------------------------+
|          Config Name           |         Config Id         |             Description             |
+================================+===========================+=====================================+
|         TestAchuth-CAC         |           4240            |              Test Cac               |
+--------------------------------+---------------------------+-------------------------------------+
| jaytest_crlstatic.symclab.com. |           1192            |    Test config for symantec crl     |
|              xml               |                           |                                     |
+--------------------------------+---------------------------+-------------------------------------+
```

#### List All the CAC Configurations in Json Format
This shows all the CAC Configurations in the Account in Json Format.
```
$ akamai cac --section default list-configurations --output-type json
[
  {
    "configurationId": 4240,
    "name": "TestAchuth-CAC",
    "description": "Test Cac",
    "currentCidrs": {
      "cidrs": [
        "0.0.0.0/0"
      ],
      "acknowledgeDate": "2020-08-04T14:04:37+0000",
      "version": 0
    },
    "proposedCidrs": []
  },
  {
    "configurationId": 1192,
    "name": "jaytest_crlstatic.symclab.com.xml",
    "description": "Test config for symantec crl",
    "currentCidrs": null,
    "proposedCidrs": []
  }
]
```

#### List of all CAC Configurations with verbose mode.
Retrieve a list of all CAC configurations along with verbose mode
```
$ akamai cac --section default --verbose list-configurations

>>>
[
  {
    "configurationId": 4240,
    "name": "TestAchuth-CAC",
    "description": "Test Cac",
    "currentCidrs": {
      "cidrs": [
        "0.0.0.0/0"
      ],
      "acknowledgeDate": "2020-08-04T14:04:37+0000",
      "version": 0
    },
    "proposedCidrs": []
  },
  {
    "configurationId": 1192,
    "name": "jaytest_crlstatic.symclab.com.xml",
    "description": "Test config for symantec crl",
    "currentCidrs": null,
    "proposedCidrs": []
  }
]
<<<

LOG: GET /client-access-control/v1/configurations 200 application/json;charset=UTF-8
+--------------------------------+---------------------------+-------------------------------------+
|          Config Name           |         Config Id         |             Description             |
+================================+===========================+=====================================+
|         TestAchuth-CAC         |           4240            |              Test Cac               |
+--------------------------------+---------------------------+-------------------------------------+
| jaytest_crlstatic.symclab.com. |           1192            |    Test config for symantec crl     |
|              xml               |                           |                                     |
+--------------------------------+---------------------------+-------------------------------------+
```

#### List of all CAC Configurations with debug mode.
This command displays list of the CAC configurations in debug mode which prints the HTTP headers.
```
$ akamai cac --section default --debug list-configurations

DEBUG:akamai.edgegrid.edgegrid:unsigned authorization header: EG1-HMAC-SHA256 client_token=akab-7akt7o5towbbkhzs-rlzj3dxkhvon6vwt;access_token=akab-7ea2zqjjsq726chb-4lcohffx5lcedzsl;timestamp=20200806T06:23:07+0000;nonce=c92f299c-819d-4b70-bf32-f220d238efe2;
DEBUG:akamai.edgegrid.edgegrid:body is ''
DEBUG:akamai.edgegrid.edgegrid:content hash is ''
DEBUG:akamai.edgegrid.edgegrid:data to sign: GET\thttps\takab-dtekh7br4mq2ao5i-rawi2krrlwirkvpa.luna.akamaiapis.net\t/client-access-control/v1/configurations\t\t\tEG1-HMAC-SHA256 client_token=akab-7akt7o5towbbkhzs-rlzj3dxkhvon6vwt;access_token=akab-7ea2zqjjsq726chb-4lcohffx5lcedzsl;timestamp=20200806T06:23:07+0000;nonce=c92f299c-819d-4b70-bf32-f220d238efe2;
DEBUG:akamai.edgegrid.edgegrid:signing key: BqxyAybKEnH5ryOXmLjdoUaE1iH3ccVgd093dp3gTGc=
DEBUG:akamai.edgegrid.edgegrid:signed authorization header: EG1-HMAC-SHA256 client_token=akab-7akt7o5towbbkhzs-rlzj3dxkhvon6vwt;access_token=akab-7ea2zqjjsq726chb-4lcohffx5lcedzsl;timestamp=20200806T06:23:07+0000;nonce=c92f299c-819d-4b70-bf32-f220d238efe2;signature=ILA6gYI+OYTcJKqH+VWv3VOPe7Qq9Q5i8+C6H2QlVfw=
DEBUG:urllib3.connectionpool:Starting new HTTPS connection (1): akab-dtekh7br4mq2ao5i-rawi2krrlwirkvpa.luna.akamaiapis.net:443
send: b'GET /client-access-control/v1/configurations HTTP/1.1\r\nHost: akab-dtekh7br4mq2ao5i-rawi2krrlwirkvpa.luna.akamaiapis.net\r\nUser-Agent: AkamaiCLI\r\nAccept-Encoding: gzip, deflate\r\nAccept: */*\r\nConnection: keep-alive\r\nAuthorization: EG1-HMAC-SHA256 client_token=akab-7akt7o5towbbkhzs-rlzj3dxkhvon6vwt;access_token=akab-7ea2zqjjsq726chb-4lcohffx5lcedzsl;timestamp=20200806T06:23:07+0000;nonce=c92f299c-819d-4b70-bf32-f220d238efe2;signature=ILA6gYI+OYTcJKqH+VWv3VOPe7Qq9Q5i8+C6H2QlVfw=\r\n\r\n'
reply: 'HTTP/1.1 200 OK\r\n'
DEBUG:urllib3.connectionpool:https://akab-dtekh7br4mq2ao5i-rawi2krrlwirkvpa.luna.akamaiapis.net:443 "GET /client-access-control/v1/configurations HTTP/1.1" 200 342
header: X-Content-Type-Options header: X-XSS-Protection header: Cache-Control header: Pragma header: Expires header: X-Frame-Options header: Content-Type header: X-Trace-Id header: Content-Length header: Date header: Connection +--------------------------------+---------------------------+-------------------------------------+
|          Config Name           |         Config Id         |             Description             |
+================================+===========================+=====================================+
|         TestAchuth-CAC         |           4240            |              Test Cac               |
+--------------------------------+---------------------------+-------------------------------------+
| jaytest_crlstatic.symclab.com. |           1192            |    Test config for symantec crl     |
|              xml               |                           |                                     |
+--------------------------------+---------------------------+-------------------------------------+

```


#### Usage of Get-configurations Command
This shows how to use get particular CAC configuration detail.
```
$ akamai cac get-configuration --h
          [or]
$ akamai cac get-configuration --help
usage: akamai-cac get-configuration [-h] [--output-file file_name]
                                    [--output-type json/text]
                                    id

positional arguments:
  id                    Config id to retrieve

optional arguments:
  -h, --help            show this help message and exit
  --output-file file_name, -f file_name
                        Save output to a file
  --output-type json/text, -t json/text
                        Output type {json, text}. Default is text
```


#### Get-configurations Command
This shows how to  get particular CAC configuration Detail in text format.
```
$akamai cac --section default get-configuration 4240
Retrieving: 4240
+--------------------------------+---------------------------+-------------------------------------+-------------------------------------+
|          Config Name           |         Config Id         |            Current CIDRs            |           Proposed CIDRs            |
+================================+===========================+=====================================+=====================================+
|         TestAchuth-CAC         |           4240            |            ['0.0.0.0/0']            |                 {}                  |
+--------------------------------+---------------------------+-------------------------------------+-------------------------------------+-+
```


#### Get-configurations Command in Json
This shows how to  get particular CAC configuration Detail in text format.
```
$akamai cac --section default get-configuration 4240 --output-type json
Retrieving: 4240
{
  "configurationId": 4240,
  "name": "TestAchuth-CAC",
  "description": "Test Cac",
  "currentCidrs": {
    "cidrs": [
      "0.0.0.0/0"
    ],
    "acknowledgeDate": "2020-08-04T14:04:37+0000",
    "version": 0
  },
  "proposedCidrs": []
}

```

#### Get-configurations output to a text file
This shows how to  get particular CAC configuration and output to a text file.
```
$akamai cac --section default get-configuration 4240 --output-file conf.txt
Retrieving: 4240
```

#### Acknowledge CIDRs Help
This shows how to use Acknowledge CIDR command.
```
akamai cac --section default acknowledge-cidr --h
usage: akamai-cac acknowledge-cidr [-h] [--output-type json/text]
                                   config_id version_id

positional arguments:
  config_id             Config id to Acknowledge
  version_id            Version id to Acknowledge

optional arguments:
  -h, --help            show this help message and exit
  --output-type json/text, -t json/text
                        Output type {json, text}. Default is text
```

####Acknowledge CIDRs
This command is used to acknowlege a CIDR Block.
```
$akamai cac --section default acknowledge-cidr 4240 1
Are you sure:[Y/N]Y
Attempting to Acknowlede the CIDR block.........
Successfully Acknowledged
```
