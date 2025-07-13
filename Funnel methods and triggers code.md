```python
import frappe
import json
from nextai.funnel.doctype.funnel_task.api import get_manual_trigger_funnels
from nextai.funnel.doctype.funnel_task.triggers.on_manual_trigger import trigger
@frappe.whitelist()
def share_loan_detail(users,doc_name,email_id,country_of_study,course_of_study,total_education_cost,loan_amount,base_url):
    redirect_link = base_url + "/app/loan-opportunity/" + doc_name
    user_list = ",".join(i for i in json.loads(users))
    client_name = frappe.get_value("Loan Opportunity",doc_name,"client_name")
    body_content = f"""
        You've received a new loan enquiry by {client_name},<br><br>
        Here's a copy of the enquiry form with all the details:<br>
        <strong>Name:</strong> {client_name}<br>
        <strong>Email ID:</strong> {email_id}<br>
        <strong>Country of Study:</strong> {country_of_study}<br>
        <strong>Course of Study:</strong> {course_of_study}<br>
        <strong>Total Education Cost:</strong> {total_education_cost}<br>
        <strong>Loan Amount:</strong> {loan_amount}<br>
        """
    variables = {
        "mail_address": user_list,
        "body":body_content,
        "button_url":redirect_link,
        "support_email": "support@edufund.in" ,
        "button_name":"Click Here"   
    }
    funnel_definitions = get_manual_trigger_funnels("Loan Opportunity")
    triggered = False
    for f_definition in funnel_definitions:
        def_data = json.loads(f_definition.data)
        if def_data['identifier'] == 'share_loan_details':
            trigger(f_definition.name,"Loan Opportunity",doc_name, variables)
            triggered = True
    if triggered:
        frappe.msgprint("Sending Mail")

    return "Emails sent successfully"


```
