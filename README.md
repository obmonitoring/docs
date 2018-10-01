# Outline

Monitoring Application - Product Specification

Challenge – to transparently monitor performance and availability of banks’ API platforms, and publish information on the same. Subsequently to allow the submission of performance data by TPPs and Banks to a single resource, and include this in the reporting function.

There are 3 elements to the solution proposed; each is dealt with separately below

API Specification and Data Store

It is envisaged that a data store be established, which will be the basis of the reporting layer discussed below. This should be supplied with data via API, whether by the client application or (in the future) by third parties/banks. In the first instance, the store should allow the inception of data related to the response times from the following v2 resources, with the expectation that measurement takes place from the time at which the request message is sent, to the completion of a successful (i.e. including data) response. It is important that this be made clear to any or


To simplify the submission of data, it is suggested that it be submitted using an average across an hourly time interval, so that a data point would have a structure akin to;

Date - Time Interval - TPP - Bank - Brand - Account Type - Account Sub Type - Resource - Endpoint - No of Requests - Average Response Time
For transactions, it is requested that a consistent filter be applied so that only the last 2 years’ worth of data is returned. 

To facilitate the submission of data, an API specification is required, and the necessary resource endpoint be restricted to only clients using (initially) Open Banking Ltd certificates or (subsequently) eIDAS certificates. Every organisation wishing to submit data to this tool will be vetted by the system owners, and the data store will require a staging area where data can be scrutinised before being assimilated into the main DB from which reporting will be driven. Finally, the data store should be built with an understanding that certain organisations may seek to licence direct access, and the API specification will need to include suitable structures for pulling this data.

It is not proposed that the data store be used to facilitate the submission of data relating to availability. This is covered below.

Client Application

The basic premise of the client application is to call the API resources, and use this process to generate data to push to the data store. There are two approaches to this.

Non-Authenticated
Validating the high level availability of the ASPSP authorisation server (AS) and resource server, and the performance of the /account-requests resource does not require a PSU-authenticated consent. Instead, the client application can create an account request which is then not acted on, but is deleted. The tool can also invoke PSU authentication portal and provide a status value against that. Where a negative response is experienced in the context of a non-authenticated interaction, the error handling and downtime logging procedure should be followed.

Authenticated

The remaining protected resources are only accessible with an authenticated consent. Consequently, per ASPSP brand, it will be necessary to obtain a range of PSU credentials which give access to the 28 different resources, where appropriate split by Account Type. For testing purposes, and to facilitate quick delivery of a product, it is proposed that testing be restricted to personal and business current accounts only.

Having obtained the necessary accounts, setup will require the likely manual complete of a range of authentication journeys, given access to the necessary consent objects which can be used to call the resources, and measure response times. It is recommended that accounts with at least 2 years of transaction data, and/or requests be limited to a 2 year time period. Once 90 day consent objects have been authenticated for each account, these can be used to spread a range of requests to each provider so that a protected resource is being called at least every 5 mins. This will give a further angle to validating availability. All account data should be truncated on receipt, but performance metrics will need to be stored and then pushed to the datastore on a regular basis. It is recommended that only consolidated results (as described above) are pushed, to ensure a degree of separation between the application log and the data store used for reporting purposes.The application logs should be available on request (whilst suitably sanitised of bank data and client credentials).

Error Handling
The purpose of the application is not to verify the correctness or conformance of responses. However, it is necessary to ensure that the layers below the API platform of each provider are actually communicating, hence each response will need to be checked to ensure that there is appropriate data in the body of the response. The accounts used to set up the account objects should be checked manually in advance to ensure that they have the requisite underlying data to avoid pushing a null response to a call to a protected resource. 

With this logic in place, in the event that a resource does not respond, or returns a response without suitable data in the payload, there should be logic to establish at what level (RS, AS, Enterprise) the failure is occuring. This may involve (should it be possible) pinging the server in question. Any service interruption should be flagged as potential downtime. Should requests to a protected resource continue to fail, downtime should start to be logged, this to be reflected in the reporting solution described below. This should be done in line with the guidance from the FCA - ‘Unplanned unavailability or a systems breakdown may be presumed to have arisen when five consecutive requests for access to information for the provision of payment initiation services or account information services are not replied to within 30 seconds.’


Reporting

Performance
A publicly accessible reporting layer should be placed on top of the data store. As opposed to the current CK solution, based on RAG status, this should focus on the performance of specific resources and trend analysis, with the ability to interrogate average performance times over given time periods, and to show trajectories. 

Once there is the sufficient maturity in market to allow other parties to submit performance data, it will need to be possible to filter on the basis of the submitting party. This is particularly important as it won’t be possible to scrutinise the payloads, requests, or account types another party has used to generate data, and thus enforce the input and output standardisation which is required for meaningful comparisons at a granular level.

Availability
Availability reporting should be based on the current experience of the client application. This should use the interactions with bank platforms to serve data immediately to a reporting layer, given a status value for the RS, AS and PSU authentication portal. Additionally, as with performance downtime/uptime per platform will have been written to a log, with a reporting layer on top of this giving the ability to display (in both graphical and tabular form) trends over various time periods, and percentages for these. Finally, the reporting layer should give users / applications the ability to subscribe to alerts to be sent out when service unavailability is detected, including to appropriate Slack Channels.

Areas to be covered in subsequent version

Payment Initiation Resources
V3 API Resources

