# FortiSOAR-Anomali
 FortiSOAR Anomali connector with extra three actions , approval Observable  reject Observable  and status of Observable by job id

Action Name                               Description

GET Status by JOB ID                      This function will check the status and retrive all require information	

Approve Observable by JOB ID              Approve Observable by Job ID, based on its ID	

Reject Observable by JOB ID               function to reject an import job, after it has been verified as 'ready to review'	


def get_import_status(config, params):
  OK_STATUS = [requests.codes.ok, requests.codes.created, requests.codes.accepted, 204]
  server_url = check_server_url(config.get("base_url"))
  endpoint = server_url + "/api/v1/importsession/" + str(params.get("job_id"))
  payload = generate_payload(config, None)
  response = request("POST", endpoint, params=payload, verify=config.get("verify_ssl"))
  if response.status_code not in OK_STATUS:
    return response.status_code
  else:
    json_results = json.loads(response.content)
    return json_results

def check_jobid(config, params):
  OK_STATUS = [requests.codes.ok, requests.codes.created, requests.codes.accepted, 204]
  server_url = check_server_url(config.get("base_url"))
  endpoint = server_url + "/api/v1/importsession/" + str(params.get("job_id"))
  payload = generate_payload(config, None)
  response = request("POST", endpoint, params=payload, verify=config.get("verify_ssl"))
  if response.status_code not in OK_STATUS:
    return "Error"
  else:
    json_results = json.loads(response.content)
    return "true"
  
  
def approve_import_job(config, params):
  import_status = check_jobid(config,params)
  OK_STATUS = [requests.codes.ok, requests.codes.created, requests.codes.accepted, 204]
  payload = generate_payload(config, None)
  if (import_status == "true"):
    server_url = check_server_url(config.get("base_url"))
    endpoint = server_url + "/api/v1/importsession/" + str(params.get("job_id"))+ "/approve_all"
    response = request("PATCH", endpoint, params=payload, verify=config.get("verify_ssl"))
    if response.status_code not in OK_STATUS:
      return "Error The Job ID Wrong"
    else:
      return response.text
  else:
      return  "An error occurred while checking the status of the import job. ID: " + str(params.get("job_id"))

def reject_import_job(config, params):
  import_status = check_jobid(config,params)
  OK_STATUS = [requests.codes.ok, requests.codes.created, requests.codes.accepted, 204]
  payload = generate_payload(config, None)
  if (import_status == "true"):
    server_url = check_server_url(config.get("base_url"))
    endpoint = server_url + "/api/v1/importsession/" + str(params.get("job_id"))
    response = request("DELETE", endpoint, params=payload, verify=config.get("verify_ssl"))
    if response.status_code not in OK_STATUS:
      return "Error The Job ID Wrong"
    else:
      return response.status_code
  else:
      return  "An error occurred while checking the status of the import job. ID: " + str(params.get("job_id"))
  
