0x19-postmortem

# Postmortem

## Issue Summary

For a total of 16 minutes on April 13, 2022 at 12:00 p.m. EAT, 100 percent of the web server was offline. At 12:16 p.m. EAT, service was restored. Two servers make up the website infrastructure. A loadbalancer controls the usage of these servers; one server was down, therefore the responsibility of service fell on the second. The main problem was a syntax issue in the nginx operating on one server's /etc/nginx/sites-available/default file. The syntax problem was a missing semicolon in the inserted line, which caused nginx to fail to start.

## Timeline

12:00pm EAT: Issue was detected. 12:06pm EAT: While using curl to check the response headers, the custom header indicating the name of the server that sent the response showed that only one server was responding to the requests 12:09pm EAT: Issue was handled by Habtamwa, the server administrator who logged into the server that was not responding. 12:10pm EAT: She ran the command sudo service nginx status to bring up a log of the current status of nginx. 12:12pm EAT: It was discovered that nginx was not running and the cause of failure was outlined in the following snippet:

*	nginx[31101]: nginx: [emerg] invalid number of arguments in "rewrite" directive in /etc/nginx/sites-available/default
* 	nginx[31101]: nginx: configuration file /etc/nginx/nginx.conf test failed 12:14pm EAT: She then opened the file stated in the first line of the snippet and added a semicolon to the rewrite directive. 12:15pm EAT: The file was saved and restarted Nginx. 12:16pm EAT: Service was restored.

## Root Cause and Resolution

The syntax problem in the rewrite directive applied to the /etc/nginx/sites-available/default file was the primary cause. This was fixed by first checking the nginx service's status. This pointed to a syntax issue in the file /etc/nginx/sites-available/defaults as the source of the problem. The file was opened, and at the conclusion of the rewrite command, a semicolon was appended. The file was then saved and nginx was restarted. This brought the service back online.

## Corrective Measures

* Ensure that status of a service whose configuration file has been altered is checked after restart/reload.
* Before closing and saving the configuration files, double-check that the correct syntax is being used.

## Link
https://sofumer.blogspot.com/2022/05/postmortem.html
