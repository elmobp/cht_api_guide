#CloudHealth API: Getting Started
CloudHealth provides API services to allow you to programmatically retrieve data from our platform. This guide will provide you a brief overview to get you started using the API, with a focus on retrieving reporting data.

##Disclaimer
The current API detailed in this document is version 0.0.1. This early API is subject to change. Future changes to this API may impact code written to previous versions of the API. We will do our best to notify users of the API of pending changes, in order to ease the migration to the new version.

##Getting an API Key
In order to make authenticated requests to the CloudHealth API service, it is necessary to get an API key. An API key is a globally unique identifier (GUID) automatically generated by CloudHealth on behalf of a user. This GUID uniquely identifies requests to a specific user. 

To request an API key, click on My Profile in your user settings.

![Report Elements](https://github.com/CloudHealth/cht_api_guide/blob/master/images/my_profile.png)

In your profile settings is a section named API Key. Click on the Generate API Key button to create your API key. The generated GUID will be saved with your user upon clicking the Update button. This GUID will need to be supplied with all future API requests.

![Report Elements](https://github.com/CloudHealth/cht_api_guide/blob/master/images/api_key.png)

To periodically rotate your API key for security reasons, just return to your profile settings and click Generate API Key to create a new key. Upon clicking update the previous key will cease working, and the new one will be in effect.

##Reporting API

###Terminology
We use specific terminology to in our API that is important to understand. These include:

- Report name - the name of the report (e.g. Instance Usage).
- Dimensions - the available members for the X-axis and categorization (e.g. by Days categorized by Reservation Type).
- Intervals - the time granularity of the report (e.g. monthly).
- Measures - the selected measures (e.g. # Instances).
- Filters - user defined filters for the report.
- Data - the data for the reports.

The below shows an example of the different elements of a report on the application:

![Report Elements](https://github.com/CloudHealth/cht_api_guide/blob/master/images/report_components.png)

###Making Your First API Request
The CloudHealth reporting API is a REST-based interface that returns JSON. It allows you to retrieve both default reports provided with the platform (“standard reports”), as well as reports you save (“custom reports”). In order to explore the API, we are going to use the standard UNIX utility curl.

The base API endpoint is https://chapi.cloudhealthtech.com.

####Retrieve List of Available Standard Reports
To retrieve a list of standard reports available through the API, run the below from a command:

```
$ curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports?api_key=<your api key>"
```

If you have Python installed on your system, you can pretty print the results with this command:

```
$ curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports?api_key=<your api key>" | python -m json.tool
```

Notice the list of available reports have a hierarchical structure. For example, under the topic “cost” are reports such as “history”, “current” and “instance”. These refer to the standard reports in the CloudHealth platform named Cost History, Current Cost and Instance Cost respectively. Each report also provides a URL to retrieve the report.

####Retrieve List of Standard Reports for a Topic
To retrieve the available reports for a specific topic, you can specify the topic in the URL. For example, the below will return a list of all standard reports available for the cost topic: 

```
$ curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports/cost?api_key=<your api key>"
```

```javascript
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

####Retrieve a List of Available Reports
By following the links above, you can get the URLs for all reports and sub-reports available in each category.  For instance, to retrieve all standard cost-related reports, you can issue the request below:

```
$ curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports/cost?api_key=<your api key>"
```
Response
```javascript
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
```

And to retrieve a list of custom reports, that is, reports you have saved, you can make the below request:

```
$ curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports/custom?api_key=<your api key>"
```

```javascript
{
    "links": {
        "Some Report": {
            "href": "https://chapi.cloudhealthtech.com/olap_reports/custom/{some-id}"
        },
        "Some Other Report": {
            "href": "https://chapi.cloudhealthtech.com/olap_reports/custom/{some-other-id}"
        },
        ...
    }
}
```

####Retrieve Data For a Standard Report
To retrieve data for a standard report, you specify its URL, which is provided with the previous requests. For example, the below will retrieve the data for the Cost History report:

```
$ curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports/cost/history?api_key=<your api key>"
```

For details on how to interpret the data returned, see the below section on Response For Report Request.

####Retrieve Data For a Custom Report

You can also retrieve data for a user saved report. To do this, you need to have the URL for the report as discussed above.  Its also possible to get the report ID from the UI. To get this identifier, go to Customer Reports, and click preview for the report you wish to retrieve.

You can identify the unique identifier from the URL:

![Report Elements](https://github.com/CloudHealth/cht_api_guide/blob/master/images/report_id.png)

By adding the report id in the below request, you can retrieve the data for this report:

```
$ curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports/custom/<report id>?api_key=<your api key>"
```

####Response Format For Report Requests
When you retrieve data for a report, the resulting json has the following major elements, each corresponding to the report terminology provided in Terminology section:

- “report” - the name of the report (string).
- “dimensions” - the available dimensions (x-axis and category) and their members for the report (array).
- “filters” - the selected filters for the report (array).
- “interval” - the selected interval for the report (string).
- “measures” - the selected measures for the report (array).
- “data” - the data for the report (array).

The data is returned in a JSON array, with each row in the array providing data for a specific member in the dimension. For example if the dimensions returned:

```javascript
"dimensions": [ 
{"Past-12-Months": [{"label": "Total", "name": "total"}, {"label": "2013-10", "name": "2013-10"}, {"label": "2013-11", "name": "2013-11"}, ... ]}, 
{"AWS-Service-Category": [{"label": "Total", "name": "total"}, { "label": "EC2 - Compute", "name": "ec2_compute" }, { "label": "EC2 - Transfer", "name": "ec2_transfer" }, ... ]}
]
```

The supporting data might look like this:

```javascript
"data": [ 
[[100, 40, 60], [60, 20, 40], [40, 20, 20], ...],
[[30, 20, 10], [20, 10, 10], [10, 0, nil], ...], 
[[70, 40, 30], [40, 20, 20], [30, 20, 10], ...], 
]
```

The first row in the data represents the data for the “Month” called “total”; the second row is data for the “Month” called “2013-10”, and so on. Within each row is the specific data that corresponds to the dimension called “AWS-Service-Category”. For example, the values [60, 20, 40] represent the “total”, “ec_compute”, and “ec2_transfer” for the month of “2013-10”.

Note: A zero denotes that there was data for the specified dimension member in the underlying data analyzed, but its aggregate total was zero. A null however denotes that there was no data for this dimension member in the underlying data analyzed.

#### Ad Hoc Queries
You are not limited to retrieving standard and saved (custom) reports, you can use the API to construct ad hoc queries.  In order to do so, however, you need to know the names of the dimensions, members, and measures available to each report type.  You can discover these by visiting the `/new` resource available under each report.  For instance, to discover the components available to create an instance usge query, you can visit:

```
$ curl -H "Accept: application/json" "https://chapi.cloudhealthtech.com/olap_reports/usage/instance/new?api_key=<your api key>"
```

This will return an array of available dimensions (including custom perspectives) and their repspective members. As well as an array of available measures.  The response will contain a label, for human consumption, and a name for API use.
```javascript
{
    "dimensions": [
        {
            "label": "Owner Perspective",
            "name": "Groupset-998"
            "members": [
                {
                    "label": "Joe",
                    "name": "1392"
                },
                {
                    "label": "Sue",
                    "name": "1388"
                },
                {
                    "label": "No Owner Found",
                    "name": "1386"
                }
            ],
        },
        {
            "label": "Function Perspective",
            "name": "Groupset-999"
            "members": [
                {
                    "label": "Zookeeper",
                    "name": "27715"
                },
                {
                    "label": "Databases",
                    "name": "27716"
                },
                ...
            ],
       },
       {
            "label": "Availability Zones",
            "name": "AWS-Availaibility-Zones"
            "members": [
                {
                    "label": "us-east-1a",
                    "name": "us-east-1a"
                },
                {
                    "label": "us-east-1b",
                    "name": "us-east-1b"
                },
                {
                    "label": "us-east-1d",
                    "name": "us-east-1d"
                }
            ],
        },
        {
            "label": "Days",
            "name": "time"
            "members": [
                {
                    "label": "2014-08-27",
                    "name": "1"
                },
                {
                    "label": "2014-08-28",
                    "name": "2"
                },
                ...
            ],
        }
        ...
    ],
    "measures": [
        {
            "label": "# Instances",
            "name": "usage_quantity"
        },
        {
            "label": "Total Cost ($)",
            "name": "cost"
        },
        {
            "label": "Compute Cost ($)",
            "name": "ec2_cost_compute"
        },
        {
            "label": "Transfer Cost ($)",
            "name": "ec2_cost_transfer"
        },
        {
            "label": "# Reservations",
            "name": "count"
        },
        {
            "label": "On-Demand Cost ($)",
            "name": "ec2_cost_on_demand"
        },
        {
            "label": "# Instance Hours",
            "name": "instance_hours"
        }
    ]
}
```

with this information in hand we can query for the EC2 Compute Cost by availabilty zone by time (monthly).

```
curl -H "Accept: application/json" 'https://chapi.cloudhealthtech.com/olap_reports/usage/instance?dimensions[]=time&dimensions[]=AWS-Availaibility-Zones&measures[]=ec2_cost_compute&interval=monthly&api_key=<your api key>
```

That is, for each dimension add a query string parameter called `dimensions[]`, and for each measure add a parameter called `measures[]`, setting each to the value as returned from the `/new` query.  In general, the dimensions available are hourly, daily, weekly, and monthly.

Note 1: On rare occasions the name CloudHealth has assigned a dimension or measure may have a typo (e.g. AWS-Availaibility-Zones above - note the extra i). It's important that you maintain these typos when making a query.
Note 2: When run from the command line you may need to escape various characters depending on your shell.

You can also filter on the measures available. For instance, to see just the EC2 Compute Cost in us-east-1a, you can supply a filter as follows:

```
curl -H "Accept: application/json" 'https://chapi.cloudhealthtech.com/olap_reports/usage/instance?dimensions[]=time&dimensions[]=AWS-Availaibility-Zones&measures[]=ec2_cost_compute&filters\[\]=AWS-Availaibility-Zones:select:us-east-1a&interval=monthly&api_key=<your api key>
```

Each filter is denoted with the `filters[]` query parameter and takes the following format: dimension-name:select|reject:member-name,member-name.  That is, a dimension name, a colon, an operator - either select or reject, another colon, and a comma separated list of members to include or exclude.  The operator `select` means you're interested in only the following members, and the operator `reject` means you're not interested in the following members.  For instance, to show the EC2 Compute Costs for all availability zones *except* us-east-1b and us-east-1d, you would issue the following query.

```
curl -H "Accept: application/json" 'https://chapi.cloudhealthtech.com/olap_reports/usage/instance?dimensions[]=time&dimensions[]=AWS-Availaibility-Zones&measures[]=ec2_cost_compute&filters\[\]=AWS-Availaibility-Zones:reject:us-east-1b,us-east-1d&interval=monthly&api_key=<your api key>
```

##Asset API
The Asset API can be used to retrieve information on assets you are currently or have historically run with AWS. It
supports three basic functions:

- List all available objects for your account
- Provide detail on an object, including its attributes and relationships
- Query objects of a specific type

###Listing Available Objects
To list the available objects with which you can interact, perform the following request supplying your API key.

```
curl "https://chapi.cloudhealthtech.com/api.json?api_key=20dbf6e0-1626-0130-2f1e-58b035ef5111"
```

###Listing Information on an Object
To list the available attributes and relations on a single object, perform the following request supplying the object name and your API key.

```
curl "https://chapi.cloudhealthtech.com/api/AwsInstance.json?api_key=20dbf6e0-1626-0130-2f1e-58b035ef5111"
```

###Querying Objects
To retrieve objects matching a specifc criteria, supply a CloudHealth Query Language (CHQL) statement with your request. CHQL is currently limited and supports the following:

[field name][operator][value]

```
curl "https://chapi.cloudhealthtech.com/api/search.json?api_key=20eaebc0-1626-0130-2f1e-58b035ef5111&name=AwsAccount&query=name='MyAccount'+and+is_private=0"
```

You can also include related objects through the _include_ directive. e.g.

```
curl "https://chapi.cloudhealthtech.com/api/search.json?api_key=20eaebc0-1626-0130-2f1e-58b035ef5111&name=AwsInstanceStatus&query=event_description<>''&include=instance"
```

##Ruby Examples
Below are some examples written in the Ruby programming language to get you started:

####Reporting API
- [Export Cost History data in CSV format](https://github.com/CloudHealth/cht_api_guide/blob/master/examples/ruby/export_csv.rb)
- [Finding the Month Over Month Cost Increase](https://github.com/CloudHealth/cht_api_guide/blob/master/examples/ruby/report_monthly_increase.rb)

####Asset API
- [Fetch Event Notifications for Instances](https://github.com/CloudHealth/cht_api_guide/blob/master/examples/ruby/event_status.rb)

##Python Examples
Below are some examples written in the Python programming language to get you started:

####Reporting API
- [Export Cost History data in CSV format](https://github.com/CloudHealth/cht_api_guide/blob/master/examples/python/export_csv.py)

##Node.js Examples
Below are some examples written in Node.js using the JavaScript programming language to get you started:

####Reporting API
- [Export Cost History data in CSV format](https://github.com/CloudHealth/cht_api_guide/blob/master/examples/node.js/export_csv.js)


##Support
If you have questions or issues using the reporting API, please contact us at support@cloudhealthtech.com.

