## Command Line Parameters for logagent

| Paramater | Description |
|-----------|-------------|
| __-f__ patterns.yml | file with pattern definitions | 
| __-y__ | prints parsed messages in YAML format to stdout|
| __-p__ | pretty json output |
| __-s__ | silent, print no logss, only throughput and memory usage on exit |
| __-t__ token | [Logsene](http://sematext.com/logsene) App Token to insert parsed records into Logsene. |
| __-g__ glob-pattern | use a [glob](https://www.npmjs.com/package/glob) pattern to watch log files e.g. -g "{/var/log/*.log,/Users/stefan/*/*.log}" |
| __--logsene-tmp-dir__  path| directory to store buffered logs during network outage |
| __-u__ UDP_PORT | starts a syslogd UDP listener on the given port to act as syslogd |
| __-n__ name | name for the source only when stdin is used, important to make multi-line patterns working on stdin because the status is tracked by the source name.| 
| __--heroku__ PORT | listens for heroku logs (http drain / framed syslog over http) |
| __--cfhttp__ PORT | listens for CloudFoundry logs (syslog over http)|
| __--rtail-port__  | forwards logs via udp to [rtail](http://rtail.org/) server 
| __--rtail-host__ hostname | [rtail](http://rtail.org/) server (UI for realtime logs), default: localhost|
| __list of files__, e.g. /var/log/*.log | watched by [tail-forever](https://www.npmjs.com/package/tail-forever) starting at end of file to watch|

The default output is line delimited JSON.

## Command Line Examples 
```
# Be Evil: parse all logs 
# stream logs to Logsene 1-Click ELK stack 
logagent -t LOGSENE_TOKEN /var/log/*.log 
# Act as syslog server on UDP and forward messages to Logsene
logagent -t LOGSENE_TOKEN -u 1514 
# Act as syslog server on UDP and write YAML formated messages to console
logagent -u 1514 -y  
```

Use a [glob](https://www.npmjs.com/package/glob) pattern to build the file list 

```
logagent -t LOGSENE_TOKEN -g "{/var/log/*.log,/opt/myapp/*.log}" 
```

Watch selective log output on console by passing logs via stdin and format in YAML

```
tail -f /var/log/access.log | logagent -y 
tail -f /var/log/system.log | logagent -f my_own_patterns.yml  -y 
```

Ship logs to rtail and Logsene to view logs in real-time in rtail and store logs in Logsene

```
# rtail don't need to be installed, logagent uses the rtail protocol
logagent -t $LOGSENE_TOKEN --rtail-host myrtailserver --rtail-port 9999 /var/log/*.log
```

Logagent can start the rtail web-server (in-process, saving memory), open browser with http://localhost:8080
```
# logagent has no dependency to rtail, to keep the package small
sudo npm i rtail -g
logagent -s -t $LOGSENE_TOKEN --rtail-web-port 8080 --rtail-port 9999 /var/log/*.log
```

And of course you can combine rtail and Logagent in the traditional way, simply connect both via unix pipes. An example with rtail and Logsene storage and charts:
![](http://g.recordit.co/usjLitb3Dd.gif)

