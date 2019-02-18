HELLO-GIT
{
    "fields":
    {
        "project":
        {
            "key": "TEST"
        },
        "parent":
        {
            "key": "TEST-101"
        },
        "summary": "Sub-task of TEST-101",
        "description": "Don't forget to do this too.",
        "issuetype":
        {
            "id": "5"
        }
    }
Let’s take a closer look at the “reporter” field from the example above:

        "reporter": {
            "self": "http://localhost:8090/jira/rest/api/2/user?username=admin",
            "name": "admin",
            "emailAddress": "admin@example.com",
            "avatarUrls": {
                "16x16": "http://localhost:8090/jira/secure/useravatar?size=small&avatarId=10062",
                "48x48": "http://localhost:8090/jira/secure/useravatar?avatarId=10062"
            },
            "displayName": "Administrator",
            "active": true
        },

resource = Resource(url + '/rest/api/2/issue/%s' % key, pool_instance=None, filters=[auth])
response = resource.get(headers = {'Content-Type' : 'application/json'})
    if response.status_int == 200:
        # Not all resources will return 200 on success. There are other success status codes. Like 204. We've read
        # the documentation though and know what to expect here.
        issue = json.loads(response.body_string())
        return issue
        
        resource = Resource(url + '/rest/api/2/issue/%s' % key, pool_instance=None, filters=[auth])
response = resource.get(headers = {'Content-Type' : 'application/json'})
    if response.status_int == 200:
        # Not all resources will return 200 on success. There are other success status codes. Like 204. We've read
        # the documentation though and know what to expect here.
        issue = json.loads(response.body_string())
        return issue

Copy
This performs a GET on the issue, checks for a successful response, and the parses the JSON response into a Python dictionary. The filters=[auth] line is how we told restkit to perform BASIC Authentication. Later on, we’ll reach into this Python dictionary to grab the data we want for our work:

fields = issue['fields']
if fields.has_key('subtasks'):
    for subtask in issue['fields']['subtasks']:
        # do work with a subtask

Copy
You can view the full source by downloading the attachment: draw-chart.py

You can see the script’s command line options using the standard command:

./draw-chart.py --help
You can test this against your JIRA site with:

./draw-chart.py --user=username --password=password --jira=<url-of-your-jira-site>
The output should look similar to:

Fetching JRADEV-1391
Fetching JRADEV-2062
Fetching JRADEV-2063
Fetching JRADEV-1107
Fetching JRADEV-112
Fetching JRADEV-1108
Fetching JRADEV-1218
Fetching JRADEV-1219
Fetching JRADEV-1220
Fetching JRADEV-1221
Fetching JRADEV-1684
Fetching JRADEV-2064
Fetching JRADEV-1390
Fetching JRADEV-1389
Fetching JRADEV-1388
Fetching JRADEV-2125
Fetching JRADEV-1264
Fetching JRADEV-1256
Writing to issue_graph.png
Open up the issue_graph.png to show an image that should look something like this:

Blue lines with arrows denote SubTasks.

<web-resource key="remote-link" name="Remote Issue Linking">
        <resource name="linker.js" type="download" location="linker.js"/>
        <context>jira.view.issue</context>
    </web-resource>
    
    jQuery(document).ready(function() {
    var i=new Date().getTime()
    jQuery("#issue_actions_container").find('.action-body a').each(function() {
        if (this.href.match(/\/browse\/[A-Z]+\-\d+$/)) {
            var split = this.href.split('/browse/')
            var base = split[0]
            var key = split[1]
            var options = { cacheContent: true, onHover: true, showDelay: 400, hideDelay: 400, closeOthers: false, width: 500 }
            var draw = function(contents, trigger, showPopup) {
                jQuery.getJSON(base + '/rest/api/latest/issue/' + key, function(data) {
                    var fields = data["fields"]
                    contents.empty()
                    contents.append(
                        "<ul class=\"item-details\">"
                        + "<li>"
                        + "<dl><dt>Summary: </dt>" + "<dd>" + fields["summary"] + "</dd></dl>"
                        + "<dl><dt>Type: </dt>" + "<dd>" + fields["issuetype"]["name"] + "</dd></dl>"
                        + "<dl><dt>Priority: </dt>" + "<dd>" + fields["priority"]["name"] + "</dd></dl>"
                        + "<dl><dt>Status: </dt>" + "<dd>" + fields["status"]["name"] + "</dd></dl>"
                        + "<dl><dt>Assignee: </dt>" + "<dd>" + fields["assignee"]["name"] + "</dd></dl>"
                        + "<dl><dt>Description: </dt>" + "<dd>" + fields["description"] + "</dd></dl>"
                        + "</li></ul>")
                    contents.append("<form id=\"add-watch\" name=\"watch\" action=\"\">")
                    jQuery("<input type=\"button\" name=\"button\" value=\"Watch\"/>").click(function() {
                        // We don't actually know our own username...and we need it to add a Watcher. So we get it from the
                        // "current user" resource
                        jQuery.getJSON(base + '/rest/auth/latest/session', function(data) {
                            jQuery.ajax({ type: "POST", url: base + "/rest/api/latest/issue/" + key + "/watchers", data: data['name'], dataType: "json", contentType: "application/json" })
                        })
                    }).appendTo(contents)
                    contents.append("</form>")
                    showPopup()
                })
            }
            AJS.InlineDialog(jQuery(this), "issue-linking-" + (i++), draw, options)
        }
    })
})
