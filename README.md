# Policy Planner API Example Guide
## Discovering Variables
When navigating to a Policy Planner ticket you can discover the following variables via the URL:
- base_url
- domain_id
- workflow_id
- ticket_id
```
https://<base_url>.firemon.com/policyplanner/#/domain/<domain_id>/workflow/<workflow_id>/ticket/<ticket_id>/view/analysis
```
## Authentication
Authentication is completed via Basic Authentication with a username and password. Ensure the used account credentials are permissioned to complete the attempted action.

## Creating a ticket

The following code will create a Policy Planner ticket and return the ID of the created ticket.

Python
```
import requests

def create_ticket():
    """
    :return: ID of ticket in integer form
    """
    payload = {
        'variables': {
            'summary': 'string',
            'requesterEmail': '<requester email>',
            'requesterName': '<requester name>'
        }
    }
    url = '<base_url>/policyplanner/api/domain/<domain_id>/workflow/<workflow_id>/packet'
    response = requests.post(
        url=url,
        auth=(<username>, <password>),
        json=payload
    )
    ticket_id = response.json()['id']
    return ticket_id

if __name__ == "__main__":
    create_ticket()
```
## Retrieving a Ticket

The following code will retrieve the JSON response for a Policy Planner ticket.

Python
```
import requests

def retrieve_ticket(ticket_id):
    """
    :param ticket_id: ID of ticket as string
    :return: JSON of response
    """
    url = '<base_url>/policyplanner/api/domain/<domain_id>/workflow/<workflow_id>/packet/' + ticket_id
    response = requests.get(
        url=url,
        auth=(<username>, <password>)
    )
    return response.json()
    
if __name__ == "__main__":
    retrieve_ticket('X') # Replace X with the Ticket ID
```	
## Assigning Ticket

The following code will assign a Policy Planner ticket to a user.

Python
```
import requests

def retrieve_ticket(ticket_id):
    """
    :param ticket_id: ID of ticket as string
    :return: JSON of response
    """
    url = '<base_url>/policyplanner/api/domain/<domain_id>/workflow/<workflow_id>/packet/' + ticket_id
    response = requests.get(
        url=url,
        auth=(<username>, <password>)
    )
    return response.json()
	
def get_workflow_packet_task_id(ticket_json):
    """
    Retrieves workflowPacketTaskId value from current stage of provided ticket
    :param ticket_json: JSON of ticket, retrieved using pull_ticket function
    :return: workflowPacketTaskId of current stage for given ticket
    """
    curr_stage = ticket_json['status']
    workflow_packet_tasks = ticket_json['workflowPacketTasks']
    for t in workflow_packet_tasks:
        if t['workflowTask']['name'] == curr_stage:
            return str(t['id'])
			
def get_workflow_task_id(ticket_json):
    """
    Retrieves workflowTaskId value from current stage of provided ticket
    :param ticket_json: JSON of ticket, retrieved using pull_ticket function
    :return: workflowTaskId of current stage for given ticket
    """
    curr_stage = ticket_json['status']
    workflow_packet_tasks = ticket_json['workflowPacketTasks']
    for t in workflow_packet_tasks:
        if t['workflowTask']['name'] == curr_stage:
            return str(t['workflowTask']['id'])

def assign_ticket(ticket_id, user_id):
    """
    :param ticket_id: ID of ticket as string
    :param user_id: ID of user_id as string
    :return:
    """
    ticket_json = retrieve_ticket(ticket_id)
    payload = user_id
    workflow_packet_task_id = get_workflow_packet_task_id(ticket_json)
    workflow_task_id = get_workflow_task_id(ticket_json)
    url = '<base_url/policyplanner/api/domain/<domain_id>/workflow/<workflow_id>/task/' + workflow_task_id + '/packet/' + ticket_id + '/packet-task/' + workflow_packet_task_id + '/assign'
    response = requests.put(
        url=url,
        auth=(<username>, <password>),
        data=payload,
        headers={'Content-Type': 'text/plain; charset=utf-8'}
    )
    return response.status_code, response.reason
	
if __name__ == "__main__":
    assign_ticket('X', 'Y') # Replace X with the Ticket ID, Replace Y with the ID of the User
```
## Updating a Ticket

The following code will update a Policy Planner ticket.

Python
```
import requests

def update_ticket(ticket_id):
    """
    :param ticket_id: Ticket ID as string
    :return: response code and reason
    """
    payload = {
        'variables': {
            'summary': 'string',
            'dueDate': '2022-01-01T05:00:00+0000', # format: %Y-%m-%dT%H:%M:%S%z
            'priority': 'LOW',  # options are LOW, MEDIUM, HIGH
            'applicationName': 'string',
            'applicationOwner': 'string',
            'businessNeed': 'string',
            'carbonCopy': ['email_1', 'email_2'],
            'customer': 'string',
            'externalTicketId': 'string',
            'notes': 'string'
        }
    }
    url = '<base_url/policyplanner/api/domain/<domain_id>/workflow/<workflow_id>/packet/' + ticket_id
    response = requests.put(
        url=url,
        auth=(<username>, <password>),
        json=payload
    )
    return response.status_code, response.reason
	
if __name__ == "__main__":
    update_ticket('X') # Replace X with the Ticket ID
```
## Completing a Ticket Task

The following code will complete a task for a Policy Planner ticket.

Python
```
import requests

def retrieve_ticket(ticket_id):
    """
    :param ticket_id: ID of ticket as string
    :return: JSON of response
    """
    url = '<base_url>/policyplanner/api/domain/<domain_id>/workflow/<workflow_id>/packet/' + ticket_id
    response = requests.get(
        url=url,
        auth=(<username>, <password>)
    )
    return response.json()
	
def get_workflow_packet_task_id(ticket_json):
    """
    Retrieves workflowPacketTaskId value from current stage of provided ticket
    :param ticket_json: JSON of ticket, retrieved using pull_ticket function
    :return: workflowPacketTaskId of current stage for given ticket
    """
    curr_stage = ticket_json['status']
    workflow_packet_tasks = ticket_json['workflowPacketTasks']
    for t in workflow_packet_tasks:
        if t['workflowTask']['name'] == curr_stage:
            return str(t['id'])
			
def get_workflow_task_id(ticket_json):
    """
    Retrieves workflowTaskId value from current stage of provided ticket
    :param ticket_json: JSON of ticket, retrieved using pull_ticket function
    :return: workflowTaskId of current stage for given ticket
    """
    curr_stage = ticket_json['status']
    workflow_packet_tasks = ticket_json['workflowPacketTasks']
    for t in workflow_packet_tasks:
        if t['workflowTask']['name'] == curr_stage:
            return str(t['workflowTask']['id'])
	
def complete_task(ticket_id, button_action):
    """
    :param ticket_id: Ticket ID as string
    :param button_action: button value as string, options are: submit, complete, autoDesign, verify, approved, rejected
    :return: Response code and reason
    """
    ticket_json = retrieve_ticket(ticket_id)
    workflow_packet_task_id = get_workflow_packet_task_id(ticket_json)
    workflow_task_id = get_workflow_task_id(ticket_json)
    url = '<base_url>/policyplanner/api/domain/<domain_id>/workflow/<workflow_id>/task/' + workflow_task_id + '/packet/' + ticket_id + '/packet-task/' + workflow_packet_task_id + '/complete?button=' + button_action
    response = requests.put(
        url=url,
        auth=(<username>, <password>),
        json={}
    )
    return response.status_code, response.reason

if __name__ == "__main__":
    complete_task('X', 'Y') # Replace X with the Ticket ID, Replace Y with the button action
```
## Adding an Add Connectivity Requirement

The following code will add an Add Connectivity requirement to a Policy Planner ticket.

Python
```
import requests

def retrieve_ticket(ticket_id):
    """
    :param ticket_id: ID of ticket as string
    :return: JSON of response
    """
    url = '<base_url>/policyplanner/api/domain/<domain_id>/workflow/<workflow_id>/packet/' + ticket_id
    response = requests.get(
        url=url,
        auth=(<username>, <password>)
    )
    return response.json()

def get_workflow_task_id(ticket_json):
    """
    Retrieves workflowTaskId value from current stage of provided ticket
    :param ticket_json: JSON of ticket, retrieved using pull_ticket function
    :return: workflowTaskId of current stage for given ticket
    """
    curr_stage = ticket_json['status']
    workflow_packet_tasks = ticket_json['workflowPacketTasks']
    for t in workflow_packet_tasks:
        if t['workflowTask']['name'] == curr_stage:
            return str(t['workflowTask']['id'])

def add_requirement(ticket_id):
    """
    :param ticket_id: ID of ticket as string
    :return: response code and reason
    """
    ticket_json = retreive_ticket(ticket_id)
    workflow_task_id = get_workflow_task_id(ticket_json)
    url = '<base_url>/policyplanner/api/policyplan/domain/<domain_id>/workflow/<workflow_id>/task/' + workflow_task_id + '/packet/' + ticket_id + '/requirements'
    payload = {
        'requirements': [
            {
                'requirementType': 'RULE',
                'changes': [],
                'childKey': 'add_access',
                'variables': {
                    'expiration': '2022-01-01T05:00:00+0000', # format: %Y-%m-%dT%H:%M:%S%z
                },
                'destinations': [
                    '<destination_1>',
		    '<destination_2>'
                ],
                'services': [
                    '<service_1>', # Example: tcp/22
                    '<service_2>'
                ],
                'sources': [
                    '<source_1>',
		    '<source_2>'
                ],
                'action': 'ACCEPT' # Options are: ACCEPT or DROP
            }
        ]
    }
    response = requests.post(
        url=url,
        auth=(<username>, <password>),
        json=payload
    )
    return response.status_code, response.reason
	
if __name__ == "__main__":
    add_requirement('X') # Replace X with the Ticket ID
```
## Retrieving Requirements

The following code will retrieve requirements from a Policy Planner ticket.
	
Python
```
import requests

def retrieve_ticket(ticket_id):
    """
    :param ticket_id: ID of ticket as string
    :return: JSON of response
    """
    url = '<base_url>/policyplanner/api/domain/<domain_id>/workflow/<workflow_id>/packet/' + ticket_id
    response = requests.get(
        url=url,
        auth=(<username>, <password>)
    )
    return response.json()

def get_workflow_task_id(ticket_json):
    """
    Retrieves workflowTaskId value from current stage of provided ticket
    :param ticket_json: JSON of ticket, retrieved using pull_ticket function
    :return: workflowTaskId of current stage for given ticket
    """
    curr_stage = ticket_json['status']
    workflow_packet_tasks = ticket_json['workflowPacketTasks']
    for t in workflow_packet_tasks:
        if t['workflowTask']['name'] == curr_stage:
            return str(t['workflowTask']['id'])
	
def get_reqs(ticket_id):
    """
    :param ticket_id: Ticket ID as string
    :return: JSON of requirements
    """
    ticket_json = retrieve_ticket(ticket_id)
    workflow_task_id = get_workflow_task_id(ticket_json)
    url = '<base_url>/policyplanner/api/policyplan/domain/<domain_id>/workflow/<workflow_id>/task/' + workflow_task_id + '/packet/' + ticket_id + '/requirements'
    response = requests.get(
        url=url,
        auth=(<username>, <password>)
    )
    return response.json()

if __name__ == "__main__":
    get_reqs('X') # Replace X with the Ticket ID
```
## Approving Requirements

The following code will approve all requirements on a Policy Planner ticket.

Python
```
import requests

def retrieve_ticket(ticket_id):
    """
    :param ticket_id: ID of ticket as string
    :return: JSON of response
    """
    url = '<base_url>/policyplanner/api/domain/<domain_id>/workflow/<workflow_id>/packet/' + ticket_id
    response = requests.get(
        url=url,
        auth=(<username>, <password>)
    )
    return response.json()

def get_workflow_task_id(ticket_json):
    """
    Retrieves workflowTaskId value from current stage of provided ticket
    :param ticket_json: JSON of ticket, retrieved using pull_ticket function
    :return: workflowTaskId of current stage for given ticket
    """
    curr_stage = ticket_json['status']
    workflow_packet_tasks = ticket_json['workflowPacketTasks']
    for t in workflow_packet_tasks:
        if t['workflowTask']['name'] == curr_stage:
            return str(t['workflowTask']['id'])
	
def get_reqs(ticket_id):
    """
    :param ticket_id: Ticket ID as string
    :return: JSON of requirements
    """
    ticket_json = retrieve_ticket(ticket_id)
    workflow_task_id = get_workflow_task_id(ticket_json)
    url = '<base_url>/policyplanner/api/policyplan/domain/<domain_id>/workflow/<workflow_id>/task/' + workflow_task_id + '/packet/' + ticket_id + '/requirements'
    response = requests.get(
        url=url,
        auth=(<username>, <password>)
    )
    return response.json()
	
def approve_req(ticket_id):
    """
    :param ticket_id: Ticket ID as string
    :return: dictionary of response codes
    """
    req_json = get_reqs(ticket_id)
    reqs = {}
    for r in req_json['results']:
        url = '<base_url>/policyplanner/api/prechangeassessments/domain/<domain_id>/workflow/<workflow_id>/packet/' + ticket_id + '/requirement/' + str(
            r['id']) + '/reviewDecision/APPROVED'
        response = requests.put(
            url=url,
            json={},
            auth=(<username>, <password>)
        )
        reqs[r['id']] = response.status_code
    return reqs
if __name__ == "__main__":
    approve_req('X') # Replace X with the Ticket ID
```
## Running and Retrieving Pre-Change Analysis

The following code will run and retrieve the Pre-Change Analysis for a Policy Planner ticket.

Python
```
import requests

def parse_controls(controls):
    """
    :param controls: Comma delimited list of controls as string
    :return: URL query as string
    """
    output = ''
    controls_list = controls.split(',')
    for c in range(0, len(controls_list)):
        if len(controls_list) > 1 and c > 0:
            output = output + '&'
        output = output + 'controlTypes=' + controls_list[c]
    return output

def run_pca(ticket_id, control_types, enable_risk_sa):
    """
    :param ticket_id: Ticket ID as string
    :param control_types: Control types as string array. Options:
    ALLOWED_SERVICES, CHANGE_WINDOW_VIOLATION, DEVICE_ACCESS_ANALYSIS, DEVICE_PROPERTY, DEVICE_STATUS,
    NETWORK_ACCESS_ANALYSIS, REGEX, REGEX_MULITPATTERN, RULE_SEARCH, RULE_USAGE, SERVICE_RISK_ANALYSIS,
    ZONE_MATRIX, ZONE_BASED_RULE_SEARCH
    :param enable_risk_sa: true or false as string
    :return: response code and reason
    """
    controls_formatted = parse_controls(control_types)
    url = <base_url> + '/policyplanner/api/prechangeassessments/domain/<domain_id>/workflow/<workflow_id>/packet/' + ticket_id + '/run?' + controls_formatted + '&enableRiskScoreAnalysis=' + enable_risk_sa
    response = requests.post(
        url=url,
        auth=(<username>, <password>)
    )
    return response.status_code, response.reason

def retrieve_pca(ticket_id):
    """
    :param ticket_id: Ticket ID as string
    :return: JSON response of PCA
    """
    url = <base_url> + '/policyplanner/api/prechangeassessments/domain/<domain_id>/workflow/<workflow_id>/packet/' + ticket_id + '/results'
    response = requests.get(
        url=url,
        auth=(<username>, <password>)
    )
    return response.json()
	
if __name__ == "__main__":
    pca = run_pca('X', 'C1,C2', 'boolean') # replace X with Ticket ID, replace C1 and C2 with Control Types
    if pca[0] == 204:
        retrieve_pca('X') # replace X with Ticket ID
    else:
        print("Error: ", pca[0], "Response: ", pca[1])
```
