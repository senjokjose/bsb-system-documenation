# BSB System overview
Please check the diagram for a high level overview
https://github.com/senjokjose/bsb-system-documenation/blob/master/BSB%20service%20c4%20diagram.pdf

### Flow
We have a file processing service download and process the BSB file from Australian payment network system ftp.
Up on receiving a new file, file processing system process the file, create a file tracking record to prevent the duplicate file processing
Stores the BSB data to the BSB database. Store the processed file to cloud storage for future auditing. Once these process is completed, it will trigger an event in the API service. This event is something like an internal  rest API call(POST) to the API server.


Up on receiving the event, API service will start evicting all the caches in the distributed cache server(Dedicated Redis )
Secondly API service starts the notification process.
It collects all the user list to be notified, store that to the redis cache(Can be useful for parallelism), Start adding each user notification request to the Active MQ

Notification services has multiple instances listen to the Active mQ user notification topic, on receiving a notification it checks with its own data base called notification database for those notification has been alraedy sent or not,
If its a duplicate. It will log and skip
If its a genuine request it will send email using Sendgrid/any email provider, We are actually sending the BSB list end point link to the customer through the email. Or we can just send a message that BSB list has been updated, Please access the list through the end point.


Customer access our BSB list through API service. Which is guarded by  APIGateway. We will be implementing auth0 or any authentication platform to secure the API access.
We also cache the BSB list, user searches in distributed cache for better performance.

### Application monitoring and analytics
We can use Kibana, Prometheus etc

### Technologies used
Java
Springboot
Postgres
ActiveMQ
Redis distributed cache
API Gateway
Elastic search Kibana
Prometheus


### Assumptions
Not dockerized yet. Changes are not completed yet.
Currently using H2 database, concurrent map hash. No active MQ instances configured

### Github links for projects
https://github.com/senjokjose/bsb-api-service

https://github.com/senjokjose/bsb-file-processing-service

https://github.com/senjokjose/bsb-notification-service












