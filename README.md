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
