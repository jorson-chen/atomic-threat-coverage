| Title                    | Alternate PowerShell Hosts Pipe       |
|:-------------------------|:------------------|
| **Description**          | Detects alternate PowerShell hosts potentially bypassing detections looking for powershell.exe |
| **ATT&amp;CK Tactic**    |  <ul><li>[TA0002: Execution](https://attack.mitre.org/tactics/TA0002)</li></ul>  |
| **ATT&amp;CK Technique** | <ul><li>[T1086: PowerShell](https://attack.mitre.org/techniques/T1086)</li></ul>  |
| **Data Needed**          | <ul><li>[DN_0020_17_windows_sysmon_PipeEvent](../Data_Needed/DN_0020_17_windows_sysmon_PipeEvent.md)</li></ul>  |
| **Trigger**              | <ul><li>[T1086: PowerShell](../Triggers/T1086.md)</li></ul>  |
| **Severity Level**       | medium |
| **False Positives**      | <ul><li>Programs using PowerShell directly without invocation of a dedicated interpreter.</li></ul>  |
| **Development Status**   | experimental |
| **References**           | <ul><li>[https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/02_execution/T1086_powershell/alternate_signed_powershell_hosts.md](https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/02_execution/T1086_powershell/alternate_signed_powershell_hosts.md)</li></ul>  |
| **Author**               | Roberto Rodriguez @Cyb3rWard0g |


## Detection Rules

### Sigma rule

```
title: Alternate PowerShell Hosts Pipe
id: 58cb02d5-78ce-4692-b3e1-dce850aae41a
description: Detects alternate PowerShell hosts potentially bypassing detections looking for powershell.exe
status: experimental
date: 2019/09/12
modified: 2019/11/10
author: Roberto Rodriguez @Cyb3rWard0g
references:
    - https://github.com/Cyb3rWard0g/ThreatHunter-Playbook/tree/master/playbooks/windows/02_execution/T1086_powershell/alternate_signed_powershell_hosts.md
tags:
    - attack.execution
    - attack.t1086
logsource:
    product: windows
    service: sysmon
detection:
    selection: 
        EventID: 17
        PipeName|startswith: '\PSHost'
    filter:
        Image|endswith: '\powershell.exe'
    condition: selection and not filter
fields:
    - ComputerName
    - User
    - Image
    - PipeName
falsepositives:
    - Programs using PowerShell directly without invocation of a dedicated interpreter.
level: medium

```





### es-qs
    
```
((EventID:"17" AND PipeName.keyword:\\\\PSHost*) AND (NOT (Image.keyword:*\\\\powershell.exe)))
```


### xpack-watcher
    
```
curl -s -XPUT -H \'Content-Type: application/json\' --data-binary @- localhost:9200/_watcher/watch/58cb02d5-78ce-4692-b3e1-dce850aae41a <<EOF\n{\n  "metadata": {\n    "title": "Alternate PowerShell Hosts Pipe",\n    "description": "Detects alternate PowerShell hosts potentially bypassing detections looking for powershell.exe",\n    "tags": [\n      "attack.execution",\n      "attack.t1086"\n    ],\n    "query": "((EventID:\\"17\\" AND PipeName.keyword:\\\\\\\\PSHost*) AND (NOT (Image.keyword:*\\\\\\\\powershell.exe)))"\n  },\n  "trigger": {\n    "schedule": {\n      "interval": "30m"\n    }\n  },\n  "input": {\n    "search": {\n      "request": {\n        "body": {\n          "size": 0,\n          "query": {\n            "bool": {\n              "must": [\n                {\n                  "query_string": {\n                    "query": "((EventID:\\"17\\" AND PipeName.keyword:\\\\\\\\PSHost*) AND (NOT (Image.keyword:*\\\\\\\\powershell.exe)))",\n                    "analyze_wildcard": true\n                  }\n                }\n              ],\n              "filter": {\n                "range": {\n                  "timestamp": {\n                    "gte": "now-30m/m"\n                  }\n                }\n              }\n            }\n          }\n        },\n        "indices": []\n      }\n    }\n  },\n  "condition": {\n    "compare": {\n      "ctx.payload.hits.total": {\n        "not_eq": 0\n      }\n    }\n  },\n  "actions": {\n    "send_email": {\n      "email": {\n        "to": "root@localhost",\n        "subject": "Sigma Rule \'Alternate PowerShell Hosts Pipe\'",\n        "body": "Hits:\\n{{#ctx.payload.hits.hits}}Hit on {{_source.@timestamp}}:\\nComputerName = {{_source.ComputerName}}\\n        User = {{_source.User}}\\n       Image = {{_source.Image}}\\n    PipeName = {{_source.PipeName}}================================================================================\\n{{/ctx.payload.hits.hits}}",\n        "attachments": {\n          "data.json": {\n            "data": {\n              "format": "json"\n            }\n          }\n        }\n      }\n    }\n  }\n}\nEOF\n
```


### graylog
    
```
((EventID:"17" AND PipeName.keyword:\\\\PSHost*) AND (NOT (Image.keyword:*\\\\powershell.exe)))
```


### splunk
    
```
((EventID="17" PipeName="\\\\PSHost*") NOT (Image="*\\\\powershell.exe")) | table ComputerName,User,Image,PipeName
```


### logpoint
    
```
((event_id="17" PipeName="\\\\PSHost*")  -(Image="*\\\\powershell.exe"))
```


### grep
    
```
grep -P '^(?:.*(?=.*(?:.*(?=.*17)(?=.*\\PSHost.*)))(?=.*(?!.*(?:.*(?=.*.*\\powershell\\.exe)))))'
```



