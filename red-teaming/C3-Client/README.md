## F-Secure's C3 Client script

This is a simple [F-Secure's C3](https://github.com/FSecureLABS/C3) client Python script offering a few functions to interact with C3 framework in an automated manner.

It connects to the C3 WebController (typically the one that's listening on port _52935_) and allows to issue API requests automating few things for us.

### Usage:

The script offers subcommands-kind of CLI interface, so after every command one can issue `--help` to get subcommand's help message.


**General help**:

```
PS D:\> py c3-client.py --help

    :: F-Secure's C3 Client - a lightweight automated companion with C3 voyages
    Mariusz B. / mgeeky, <mb@binary-offensive.com>

usage:
Usage: ./c3-client.py [options] <host> <command> [...]

positional arguments:
  host                  C3 Web API host:port
  {alarm,list,get,ping,channel}
                        command help
    alarm               Alarm options
    list                List options
    get                 Get options
    ping                Ping Relays
    channel             Send Channel-specific command

optional arguments:
  -h, --help            show this help message and exit
  -v, --verbose         Display verbose output.
  -d, --debug           Display debug output.
  -f {json,text}, --format {json,text}
                        Output format. Can be JSON or text (default).
  -A user:pass, --httpauth user:pass
                        HTTP Basic Authentication (user:pass)
```

**Example of a sub-help**:

```
PS D:\> py c3-client.py -f text http://192.168.0.200:52935 alarm relay --help

    :: F-Secure's C3 Client - a lightweight automated companion with C3 voyages
    Mariusz B. / mgeeky, <mb@binary-offensive.com>

usage: Usage: ./c3-client.py [options] <host> <command> [...] alarm relay [-h] [-e EXECUTE] [-x WEBHOOK] [-g gateway_id]

optional arguments:
  -h, --help            show this help message and exit
  -e EXECUTE, --execute EXECUTE
                        If new Relay checks in - execute this command. Use following placeholders in your command: <computerName>, <userName>,
                        <domain>, <isElevated>, <osVersion>, <processId>, <relayName>, <relayId>, <buildId>, <timestamp> to customize executed
                        command's parameters. Example: powershell -c "Add-Type -AssemblyName System.Speech; $synth = New-Object -TypeName
                        System.Speech.Synthesis.SpeechSynthesizer; $synth.Speak('New Relay just checked-in
                        <domain>/<userName>@<computerName>')"
  -x WEBHOOK, --webhook WEBHOOK
                        Trigger a Webhook (HTTP POST request) to this URL whenever a new Relay checks-in. The request will contain JSON message
                        with all the fields available, mentioned in --execute option.
  -g gateway_id, --gateway-id gateway_id
                        ID (or Name) of the Gateway which Relays should be returned. If not given, will result all relays from all gateways.
```

Currently, following commands are supported:

- `list`
    - `gateways` - list gateways in either JSON or text format
    - `relays` - list relays in either JSON or text format

- `get`
    - `gateway` - get gateway details in text or JSON format
    - `relay` - get relay details in text or JSON format

- `alarm`
    - `relay` - trigger an alarm whenever a new Relay checks-in on a gateway

- `ping` - ping selected Relays

- `channel` - channel-specific commands
    - `all`
        - `clear` - Clear message queue of every supported channel at once
    - `mattermost`
        - `clear` - Clear Mattermost's channel messages to improve bandwidth
    - `ldap`
        - `clear` - Clear LDAP attribute to improve bandwidth
    - `mssql`
        - `clear` - Clear DB Table entries to improve bandwidth
    - `uncsharefile`
        - `clear` - Remove all message files to improve bandwidth
    - `dropbox`
        - `clear` - Remove All Files to improve bandwidth
    - `github`
        - `clear` - Remove All Files to improve bandwidth
    - `googledrive`
        - `clear` - Remove All Files to improve bandwidth


### Example Usage

**Example 1**

This example shows how to keep all of your Relays pinged every 45 seconds:

```
PS D:\> py c3-client.py http://192.168.0.200:52935 ping -k 45

    :: F-Secure's C3 Client - a lightweight automated companion with C3 voyages
    Mariusz B. / mgeeky, <mb@binary-offensive.com>

[.] Sending a ping every 45 seconds.
[.] Pinged relay: matter4    from gateway  gate4
[.] Pinged relay: mssql1     from gateway  gate4
[.] Pinged relay: ldap9      from gateway  gate4
[.] Pinged relay: mssql1     from gateway  gate4
[+] Pinged 4 active relays.

[.] Sending a ping every 45 seconds.
[.] Pinged relay: matter4    from gateway  gate4
[.] Pinged relay: mssql1     from gateway  gate4
[.] Pinged relay: ldap9      from gateway  gate4
[.] Pinged relay: mssql1     from gateway  gate4
[+] Pinged 4 active relays.

```

**Example 2**

Ever suffered from a poor C3 bandwidth or general performance? Worry not - you can easily clear/remove message queues from all of your channels with this simple trick:

```
PS D:\> py .\c3-client.py -f text http://192.168.0.200:52935 channel all clear

    :: C3 Client - a lightweight automated companion with C3 voyages
    Mariusz B. / mgeeky, <mb@binary-offensive.com>

[.] LDAP: Clearing messages queue...
[+] Cleared LDAP attribute value on C3 channel 3 on Relay matter4 on gateway gate4
[+] Cleared LDAP attribute value on C3 channel 8001 on Relay matter4 on gateway gate4
[+] Cleared LDAP attribute value on C3 channel 8000 on Relay ldap9 on gateway gate4

[.] MSSQL: Clearing messages queue...
[+] Cleared MSSQL Table on C3 channel 4 on Relay matter4 on gateway gate4
[+] Cleared MSSQL Table on C3 channel 8002 on Relay matter4 on gateway gate4
[+] Cleared MSSQL Table on C3 channel 8003 on Relay matter4 on gateway gate4
[+] Cleared MSSQL Table on C3 channel 8000 on Relay mssql1 on gateway gate4
[+] Cleared MSSQL Table on C3 channel 8000 on Relay mssql1 on gateway gate4

[.] Mattermost: Clearing messages queue...
[+] Purged all messages from Mattermost C3 channel 8000 on Relay matter4 on gateway gate4
[+] Purged all messages from Mattermost C3 channel 8000 on Relay matter4 on gateway gate4
[+] Purged all messages from Mattermost C3 channel 1 on gateway gate4
[+] Purged all messages from Mattermost C3 channel 4 on gateway gate4
[+] Purged all messages from Mattermost C3 channel 14 on gateway gate4

[.] GoogleDrive: Clearing messages queue...
[-] No channels could be found to receive GoogleDrive remove all message files command.

[.] Github: Clearing messages queue...
[-] No channels could be found to receive Github remove all message files command.

[.] Dropbox: Clearing messages queue...
[-] No channels could be found to receive Dropbox remove all message files command.

[.] UncShareFile: Clearing messages queue...
[-] No channels could be found to receive UncShareFile remove all message files command.

```

**Example 3**

In this example setup an alarm that triggers upon new Relay checking-in. Whenever that happens, a command is executed with placeholders that will be substituted with values extracted from Relay's metadata:

```
PS D:\> py c3-client.py http://192.168.0.200:52935 alarm relay -g gate4 --execute "powershell -file speak.ps1 -message \`"New C3 Relay Inbound: <domain>/<userName>, computer: <computerName>\`""

    :: F-Secure's C3 Client - a lightweight automated companion with C3 voyages
    Mariusz B. / mgeeky, <mb@binary-offensive.com>

[.] Entering infinite-loop awaiting for new Relays...
[+] New Relay checked-in!
    Relay 5:              matter4
        Relay ID:         70a6f7c456f049c8
        Build ID:         795f
        Is active:        True                  (+)
        Timestamp:        2021-03-24 04:14:34
        Host Info:
            Computer:     JUMPBOX
            Domain:       CONTOSO
            User Name:    alice
            Is elevated:  False
            OS Version:   Windows 10.0 Server SP: 0.0 Build 14393
            Process ID:   4092

    Channels:
        Gateway Return Channel (GRC) 1:
            Jitter:      3.5 ... 6.5
            Properties:
                Name:    Output ID
                Value:   3UM2G2TW

                Name:    Input ID
                Value:   fftuO5py

                Name:    Mattermost Server URL
                Value:   http://192.168.0.210:8080

                Name:    Mattermost Team Name
                Value:   foobar

                Name:    Mattermost Access Token
                Value:   c3g7sokucbgidgxxxxxxxxxx

                Name:    Channel name
                Value:   x26vg0

                Name:    User-Agent Header
                Value:   Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko)

[.] Executing command: powershell -file speak.ps1 -message "New C3 Relay Inbound: CONTOSO/alice, computer: JUMPBOX"

```
