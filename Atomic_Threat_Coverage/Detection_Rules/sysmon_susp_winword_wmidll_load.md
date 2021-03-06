| Title                    | Windows Mangement Instrumentation DLL Loaded Via Microsoft Word       |
|:-------------------------|:------------------|
| **Description**          | Detects DLL's Loaded Via Word Containing VBA Macros Executing WMI Commands |
| **ATT&amp;CK Tactic**    |  <ul><li>[TA0002: Execution](https://attack.mitre.org/tactics/TA0002)</li></ul>  |
| **ATT&amp;CK Technique** | <ul><li>[T1047: Windows Management Instrumentation](https://attack.mitre.org/techniques/T1047)</li></ul>  |
| **Data Needed**          | <ul><li>[DN_0011_7_windows_sysmon_image_loaded](../Data_Needed/DN_0011_7_windows_sysmon_image_loaded.md)</li></ul>  |
| **Trigger**              | <ul><li>[T1047: Windows Management Instrumentation](../Triggers/T1047.md)</li></ul>  |
| **Severity Level**       | high |
| **False Positives**      | <ul><li>Possible. Requires further testing.</li></ul>  |
| **Development Status**   | experimental |
| **References**           | <ul><li>[https://medium.com/threatpunter/detecting-adversary-tradecraft-with-image-load-event-logging-and-eql-8de93338c16](https://medium.com/threatpunter/detecting-adversary-tradecraft-with-image-load-event-logging-and-eql-8de93338c16)</li><li>[https://www.carbonblack.com/2019/04/24/cb-tau-threat-intelligence-notification-emotet-utilizing-wmi-to-launch-powershell-encoded-code/](https://www.carbonblack.com/2019/04/24/cb-tau-threat-intelligence-notification-emotet-utilizing-wmi-to-launch-powershell-encoded-code/)</li><li>[https://media.cert.europa.eu/static/SecurityAdvisories/2019/CERT-EU-SA2019-021.pdf](https://media.cert.europa.eu/static/SecurityAdvisories/2019/CERT-EU-SA2019-021.pdf)</li></ul>  |
| **Author**               | Michael R. (@nahamike01) |


## Detection Rules

### Sigma rule

```
title: Windows Mangement Instrumentation DLL Loaded Via Microsoft Word
id: a457f232-7df9-491d-898f-b5aabd2cbe2f
status: experimental
description: Detects DLL's Loaded Via Word Containing VBA Macros Executing WMI Commands
references:
    - https://medium.com/threatpunter/detecting-adversary-tradecraft-with-image-load-event-logging-and-eql-8de93338c16
    - https://www.carbonblack.com/2019/04/24/cb-tau-threat-intelligence-notification-emotet-utilizing-wmi-to-launch-powershell-encoded-code/
    - https://media.cert.europa.eu/static/SecurityAdvisories/2019/CERT-EU-SA2019-021.pdf
author: Michael R. (@nahamike01)
date: 2019/12/26
tags:
    - attack.execution
    - attack.t1047
logsource:
    product: windows
    service: sysmon
detection:
    selection:
        EventID: 7
        Image:
            - '*\winword.exe'
            - '*\powerpnt.exe'
            - '*\excel.exe'
            - '*\outlook.exe'
        ImageLoaded:
            - '*\wmiutils.dll'
            - '*\wbemcomn.dll'
            - '*\wbemprox.dll'
            - '*\wbemdisp.dll'
            - '*\wbemsvc.dll'
    condition: selection
falsepositives:
    - Possible. Requires further testing.
level: high

```





### es-qs
    
```
(EventID:"7" AND Image.keyword:(*\\\\winword.exe OR *\\\\powerpnt.exe OR *\\\\excel.exe OR *\\\\outlook.exe) AND ImageLoaded.keyword:(*\\\\wmiutils.dll OR *\\\\wbemcomn.dll OR *\\\\wbemprox.dll OR *\\\\wbemdisp.dll OR *\\\\wbemsvc.dll))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/a457f232-7df9-491d-898f-b5aabd2cbe2f <<EOF\n{\n  "metadata": {\n    "title": "Windows Mangement Instrumentation DLL Loaded Via Microsoft Word",\n    "description": "Detects DLL\'s Loaded Via Word Containing VBA Macros Executing WMI Commands",\n    "tags": [\n      "attack.execution",\n      "attack.t1047"\n    ],\n    "query": "(EventID:\\"7\\" AND Image.keyword:(*\\\\\\\\winword.exe OR *\\\\\\\\powerpnt.exe OR *\\\\\\\\excel.exe OR *\\\\\\\\outlook.exe) AND ImageLoaded.keyword:(*\\\\\\\\wmiutils.dll OR *\\\\\\\\wbemcomn.dll OR *\\\\\\\\wbemprox.dll OR *\\\\\\\\wbemdisp.dll OR *\\\\\\\\wbemsvc.dll))"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "(EventID:\\"7\\" AND Image.keyword:(*\\\\\\\\winword.exe OR *\\\\\\\\powerpnt.exe OR *\\\\\\\\excel.exe OR *\\\\\\\\outlook.exe) AND ImageLoaded.keyword:(*\\\\\\\\wmiutils.dll OR *\\\\\\\\wbemcomn.dll OR *\\\\\\\\wbemprox.dll OR *\\\\\\\\wbemdisp.dll OR *\\\\\\\\wbemsvc.dll))",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'Windows Mangement Instrumentation DLL Loaded Via Microsoft Word\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(EventID:"7" AND Image.keyword:(*\\\\winword.exe *\\\\powerpnt.exe *\\\\excel.exe *\\\\outlook.exe) AND ImageLoaded.keyword:(*\\\\wmiutils.dll *\\\\wbemcomn.dll *\\\\wbemprox.dll *\\\\wbemdisp.dll *\\\\wbemsvc.dll))
```


### splunk
    
```
(EventID="7" (Image="*\\\\winword.exe" OR Image="*\\\\powerpnt.exe" OR Image="*\\\\excel.exe" OR Image="*\\\\outlook.exe") (ImageLoaded="*\\\\wmiutils.dll" OR ImageLoaded="*\\\\wbemcomn.dll" OR ImageLoaded="*\\\\wbemprox.dll" OR ImageLoaded="*\\\\wbemdisp.dll" OR ImageLoaded="*\\\\wbemsvc.dll"))
```


### logpoint
    
```
(event_id="7" Image IN ["*\\\\winword.exe", "*\\\\powerpnt.exe", "*\\\\excel.exe", "*\\\\outlook.exe"] ImageLoaded IN ["*\\\\wmiutils.dll", "*\\\\wbemcomn.dll", "*\\\\wbemprox.dll", "*\\\\wbemdisp.dll", "*\\\\wbemsvc.dll"])
```


### grep
    
```
grep -P '^(?:.*(?=.*7)(?=.*(?:.*.*\\winword\\.exe|.*.*\\powerpnt\\.exe|.*.*\\excel\\.exe|.*.*\\outlook\\.exe))(?=.*(?:.*.*\\wmiutils\\.dll|.*.*\\wbemcomn\\.dll|.*.*\\wbemprox\\.dll|.*.*\\wbemdisp\\.dll|.*.*\\wbemsvc\\.dll)))'
```



