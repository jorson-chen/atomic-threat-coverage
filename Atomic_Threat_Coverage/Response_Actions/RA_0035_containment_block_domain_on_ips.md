| Title                       | RA_0035_containment_block_domain_on_ips         |
|:----------------------------|:--------------------|
| **Description**             | Block a domain name with IPS   |
| **Author**                  | @atc_project        |
| **Creation Date**           | 31.01.2019 |
| **Stage**                   | containment         |
| **Automation**              | None |
| **References**              | None |
| **Linked Response Actions** | None |
| **Linked Analytics**        |<ul><li>MS_IPS</li></ul> |


### Workflow

Block domain on IPS using native filtering functionality.
Warning: 
- If not all corporate hosts access internet through the IPS, this Response Action cannot guarantee containment of threat.
- Be careful blocking domain names. Make sure it's not cloud provider or hoster. In this case you have to use blocking by URL something more specific.
