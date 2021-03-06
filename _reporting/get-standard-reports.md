---
title: List Reports of Specific Type
type: get
description: Retrieve the list of available Standard OLAP reports of a specify type.
position: 4
endpoint: https://chapi.cloudhealthtech.com/olap_reports/:report-type
parameters:
  - name:
    content:
content_markdown: |-
  Retrieving a list of all reports of a specific type, such as `/cost`, `/custom`, `/performance`, or `/usage` is a two-step process.

  1. Get the endpoints for all types of Standard reports.

     `curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports?api_key=<your_api_key>"`

     This query returns the following response.

     ```
     {
       "links": {
         "cost": {
           "href": "https://chapi.cloudhealthtech.com/olap_reports/cost"
         },
         "custom": {
           "href": "https://chapi.cloudhealthtech.com/olap_reports/custom"
         },
         "performance": {
           "href": "https://chapi.cloudhealthtech.com/olap_reports/performance"
         },
         "usage": {
           "href": "https://chapi.cloudhealthtech.com/olap_reports/usage"
         }
       }
     }
     ```

  2. Query the endpoint for the type of report from this response and get a list of all reports of that type.

     `curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports/cost?api_key=<your_api_key>"`
right_code_blocks:
  - code_block: |-
      curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports?api_key=<your_api_key>"

      curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports/cost?api_key=<your_api_key>"`
    title: Request
    language: bash
  - code_block: |-
      {
        "links": {
            "amortized": {
                "href": "https://chapi.cloudhealthtech.com/olap_reports/cost/amortized"
            },
            "current": {
                "href": "https://chapi.cloudhealthtech.com/olap_reports/cost/current"
            },
            "history": {
                "href": "https://chapi.cloudhealthtech.com/olap_reports/cost/history"
            },
            "instance": {
                "href": "https://chapi.cloudhealthtech.com/olap_reports/cost/instance"
            },
            "rds": {
                "href": "https://chapi.cloudhealthtech.com/olap_reports/cost/rds"
            },
            "ri_amortization": {
                "href": "https://chapi.cloudhealthtech.com/olap_reports/cost/ri_amortization"
            },
            "s3": {
                "href": "https://chapi.cloudhealthtech.com/olap_reports/cost/s3"
            },
            "volume": {
                "href": "https://chapi.cloudhealthtech.com/olap_reports/cost/volume"
            }
        }
      }
    title: Response
    language: json
---
