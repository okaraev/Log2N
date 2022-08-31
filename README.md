# Log to Notification microservices

Log to Notification (briefly Log2N) is for notifying clients about their logs with defined parameters. Log2N is designed to be scalable. Its design also ensures high consistency in notification delivery. Thus, Log2N can be used in high load applications, where delivery is important.

---
## Services
There are 4 services:
* [Client Config](https://github.com/okaraev/Log2N_Config)
* [Log Gateway](https://github.com/okaraev/Log2N_Gateway)
* [Config Matcher](https://github.com/okaraev/Log2N_Matcher)
* [Notifier](https://github.com/okaraev/Log2N_Notifier)
---
## Client Config Service

This is a service for storing client configurations. The service provides an api interface for adding, changing, deleting configurations. By default datastore is mongodb, but it can be easily changed. A simple client configuration is as follows:

```
{
    "Name": "DevTeam01Config01",
    "Team": "Team01",
    "LogPattern": "cannot process request",
    "LogSeverity": "Critical",
    "NotificationMethod": "Telegram",
    "LogLogic": "AND",
    "NotificationRecipient": [ "123456789","987654321" ],
    "HoldTime": 3,
    "RetryCount": 5
}
```
Although the Service stores clients' configurations, it is less critical service, because it doesn't affect the notification delivery. Thats why Client Config service is labeled as a Tier 2 service.

---
## Log Gateway Service

This service is responsible for receiving logs from clients and send them to the Config Matcher service. Here is a sample Log:
```
{
    "Team": "DevTeam01",
    "Severity": "Critical",
    "Log": "Got an error: cannot process request, calculator service is down"
}
```
Log Gateway service is critical from the client perspective, therefore it is labeled as a Tier 1 service.

---
## Config Matcher Service

It is a service, which checks for matches between clients logs and their configurations. A single log could be matched with many configurations. In case of a match the service sends it to the Notifier service for further processing.
It is high critical service and its label is Tier 1.

---
## Notifier Service

This services responsibility is delivery. After receiving notification the service delivers it if it has a notification method, defined in the notification. By default it has only one notification method. But it can be extended with custom notification methods. The service is labeled as Tier 1.