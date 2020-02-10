# structured-logging-2-system

Structure logging 2 system is an idea of distributing work across machines by using log lines to dictate where the flow of your application goes. You write a linearized view of your entire process into a CSV file and specify what machine each line should run on. While logging you specify when you are finished running a log line and the next system in the log is called.

# Generating codebases

Ideally it should be possible to change the CSV file and change the code where things get executed from but this will take more work.

# CSV file

Have a csv file with your log lines in it and where it should run.

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

# Example


```
index,Checking if signed in,server
index,Generating list of questions,server
index,Sending sign in email,emailservice

```
```
class Client:
    def __init__(self):
        pass

    def request(self, url, context):
            print("We need to make a call to {}".format(url))
            print("Passing context {} to {}".format(context, url))

class StructuredLog:
    def __init__(self, filename, client):
        self.client = client
        self.lines_index = {}
        self.lines = []
        with open(filename, newline='') as csvfile:
            for line in map(Line._make, csv.reader(csvfile)):
                self.lines.append(line.line)
                self.lines_index[line.line] = line

    def emit(self, line):
        self.line = line
        print(line)

    def next(self, context):
        current_line_index = self.lines.index(self.line)
        current_line = self.lines[current_line_index]
        next_line_index = current_line_index + 1
        next_line = self.lines[next_line_index]
        self.line = next_line
        # service is different
        if self.lines_index[next_line].server != self.lines_index[current_line].server:
            self.client.request(self.lines_index[next_line].server, context)
```

# Usage

```
sl = StructuredLog("log.csv", Client())
sl.emit("Checking if signed in")
signed_in, username, user_email, email_token, login_token = check_signed_in()
sl.next({"username": username})

sl.emit("Generating list of questions")
questions = []
# do fancy stuf with questions
sl.next({"questions":[]})

```

```
Checking if signed in
Generating list of questions
We need to make a call to emailservice
Passing context {"questions": []} to emailservice

```
