# structured-logging-2-system

Structure logging 2 system is how you use structured logging to handle request handling between microservices.

# CSV file

Have a csv file with your log lines in it, for example:

```
Request	Log	Machine
login	User attempted login as :username and #password	client
login	Received request to login as :username	server
login	Testing password for :username	server
login	Setting session for :username	server
login	Finish handling user login	server
login	Login successful	client
feed	Fetching feed information	client
feed	Retrieving feed information	server

```
