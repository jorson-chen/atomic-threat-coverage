| Title                    | GAC DLL Loaded Via Office Applications       |
|:-------------------------|:------------------|
| **Description**          | Detects any GAC DLL being loaded by an Office Product |
| **ATT&amp;CK Tactic**    |  <ul><li>[TA0001: Initial Access](https://attack.mitre.org/tactics/TA0001)</li></ul>  |
| **ATT&amp;CK Technique** | <ul><li>[T1193: Spearphishing Attachment](https://attack.mitre.org/techniques/T1193)</li></ul>  |
| **Data Needed**          | <ul><li>[DN_0011_7_windows_sysmon_image_loaded](../Data_Needed/DN_0011_7_windows_sysmon_image_loaded.md)</li></ul>  |
| **Trigger**              | <ul><li>[T1193: Spearphishing Attachment](../Triggers/T1193.md)</li></ul>  |
| **Severity Level**       | high |
| **False Positives**      | <ul><li>Alerts on legitimate macro usage as well, will need to filter as appropriate</li></ul>  |
| **Development Status**   | experimental |
| **References**           | <ul><li>[https://medium.com/threatpunter/detecting-adversary-tradecraft-with-image-load-event-logging-and-eql-8de93338c16](https://medium.com/threatpunter/detecting-adversary-tradecraft-with-image-load-event-logging-and-eql-8de93338c16)</li></ul>  |
| **Author**               | Antonlovesdnb |


## Detection Rules

### Sigma rule

```
title: GAC DLL Loaded Via Office Applications
id: 90217a70-13fc-48e4-b3db-0d836c5824ac
status: experimental
description: Detects any GAC DLL being loaded by an Office Product
references:
    - https://medium.com/threatpunter/detecting-adversary-tradecraft-with-image-load-event-logging-and-eql-8de93338c16
author: Antonlovesdnb
date: 2020/02/19
tags:
    - attack.initial_access
    - attack.t1193
logsource:
    product: windows
    service: sysmon
detection:
    selection:
        EventID: 7
        Image:
            - '*\winword.exe*'
            - '*\powerpnt.exe*'
            - '*\excel.exe*'
            - '*\outlook.exe*'
        ImageLoaded:
            - '*C:\Windows\Microsoft.NET\assembly\GAC_MSIL*'
    condition: selection
falsepositives:
    - Alerts on legitimate macro usage as well, will need to filter as appropriate
level: high

```





### es-qs
    
```
(EventID:"7" AND Image.keyword:(*\\\\winword.exe* OR *\\\\powerpnt.exe* OR *\\\\excel.exe* OR *\\\\outlook.exe*) AND ImageLoaded.keyword:(*C\\:\\\\Windows\\\\Microsoft.NET\\\\assembly\\\\GAC_MSIL*))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/90217a70-13fc-48e4-b3db-0d836c5824ac <<EOF\n{\n  "metadata": {\n    "title": "GAC DLL Loaded Via Office Applications",\n    "description": "Detects any GAC DLL being loaded by an Office Product",\n    "tags": [\n      "attack.initial_access",\n      "attack.t1193"\n    ],\n    "query": "(EventID:\\"7\\" AND Image.keyword:(*\\\\\\\\winword.exe* OR *\\\\\\\\powerpnt.exe* OR *\\\\\\\\excel.exe* OR *\\\\\\\\outlook.exe*) AND ImageLoaded.keyword:(*C\\\\:\\\\\\\\Windows\\\\\\\\Microsoft.NET\\\\\\\\assembly\\\\\\\\GAC_MSIL*))"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "(EventID:\\"7\\" AND Image.keyword:(*\\\\\\\\winword.exe* OR *\\\\\\\\powerpnt.exe* OR *\\\\\\\\excel.exe* OR *\\\\\\\\outlook.exe*) AND ImageLoaded.keyword:(*C\\\\:\\\\\\\\Windows\\\\\\\\Microsoft.NET\\\\\\\\assembly\\\\\\\\GAC_MSIL*))",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'GAC DLL Loaded Via Office Applications\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}{{_source}}\\n================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
(EventID:"7" AND Image.keyword:(*\\\\winword.exe* *\\\\powerpnt.exe* *\\\\excel.exe* *\\\\outlook.exe*) AND ImageLoaded.keyword:(*C\\:\\\\Windows\\\\Microsoft.NET\\\\assembly\\\\GAC_MSIL*))
```


### splunk
    
```
(EventID="7" (Image="*\\\\winword.exe*" OR Image="*\\\\powerpnt.exe*" OR Image="*\\\\excel.exe*" OR Image="*\\\\outlook.exe*") (ImageLoaded="*C:\\\\Windows\\\\Microsoft.NET\\\\assembly\\\\GAC_MSIL*"))
```


### logpoint
    
```
(event_id="7" Image IN ["*\\\\winword.exe*", "*\\\\powerpnt.exe*", "*\\\\excel.exe*", "*\\\\outlook.exe*"] ImageLoaded IN ["*C:\\\\Windows\\\\Microsoft.NET\\\\assembly\\\\GAC_MSIL*"])
```


### grep
    
```
grep -P '^(?:.*(?=.*7)(?=.*(?:.*.*\\winword\\.exe.*|.*.*\\powerpnt\\.exe.*|.*.*\\excel\\.exe.*|.*.*\\outlook\\.exe.*))(?=.*(?:.*.*C:\\Windows\\Microsoft\\.NET\\assembly\\GAC_MSIL.*)))'
```



