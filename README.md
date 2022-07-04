\# FortiSOAR-Anomali

` `FortiSOAR Anomali connector with extra three actions , approval Observable  reject Observable  and status of Observable by job id

|Action Name|Description|
| :- | :- |
|GET Status by JOB ID|This function will check the status and retrieve all require information|
|Approve Observable by JOB ID|Approve Observable based on its ID|
|Reject Observable by JOB ID|This function to reject an import job, after it has been verified as 'ready to review'|


def get\_import\_status(config, params):

`  	`OK\_STATUS = [requests.codes.ok, requests.codes.created, requests.codes.accepted, 204]

`	`server\_url = check\_server\_url(config.get("base\_url"))

`	`endpoint = server\_url + "/api/v1/importsession/" + str(params.get("job\_id"))

payload = generate\_payload(config, None)

`	`response = request("POST", endpoint, params=payload, verify=config.get("verify\_ssl"))

`	`if response.status\_code not in OK\_STATUS:

`		`return response.status\_code

`	`else:

`		`json\_results = json.loads(response.content)

`		`return json\_results


def check\_jobid(config, params):

`	`OK\_STATUS = [requests.codes.ok, requests.codes.created, requests.codes.accepted, 204]

`	`server\_url = check\_server\_url(config.get("base\_url"))

`	`endpoint = server\_url + "/api/v1/importsession/" + str(params.get("job\_id"))

`	`payload = generate\_payload(config, None)

`	`response = request("POST", endpoint, params=payload, verify=config.get("verify\_ssl"))

`	`if response.status\_code not in OK\_STATUS:

`		`return "Error"

`	`else:

`		`json\_results = json.loads(response.content)

`    		`return "true"

def approve\_import\_job(config, params):

`	  `import\_status = check\_jobid(config,params)

OK\_STATUS = [requests.codes.ok, requests.codes.created, requests.codes.accepted, 204]

`  	`payload = generate\_payload(config, None)

` 	`if (import\_status == "true"):

`   	 	`server\_url = check\_server\_url(config.get("base\_url"))

`    	`endpoint = server\_url + "/api/v1/importsession/" + str(params.get("job\_id"))+ "/approve\_all"

`    		`response = request("PATCH", endpoint, params=payload, verify=config.get("verify\_ssl"))

`    		`if response.status\_code not in OK\_STATUS:

`      			`return "Error The Job ID Wrong"

`    		`else:

`      			`return response.text

`  	`else:

return  "An error occurred while checking the status of the import job. ID: " + str(params.get("job\_id"))

def reject\_import\_job(config, params):

` 	 `import\_status = check\_jobid(config,params)

` 	 `OK\_STATUS = [requests.codes.ok, requests.codes.created, requests.codes.accepted, 204]

`  	`payload = generate\_payload(config, None)

` 	 `if (import\_status == "true"):

`    		`server\_url = check\_server\_url(config.get("base\_url"))

`   		 `endpoint = server\_url + "/api/v1/importsession/" + str(params.get("job\_id"))

`   		 `response = request("DELETE", endpoint, params=payload, verify=config.get("verify\_ssl"))

`    		`if response.status\_code not in OK\_STATUS:

`      			`return "Error The Job ID Wrong"

`  		  `else:

`     			 `return response.status\_code

`  	`else:

return  "An error occurred while checking the status of the import job. ID: " + str(params.get("job\_id"))


