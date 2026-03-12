# Idempotent CRM Automation

## **Overview**

This project demonstrates a fault-tolerant lead processing workflow between GoHighLevel (GHL) and Slack built in n8n.
The workflow is designed to safely process high-value leads while preventing duplicate actions caused by webhook retries, repeated form submissions, or API instability.

### **Key goals of the system:**
- Prevent duplicate lead processing (idempotency)
- Maintain CRM data hygiene
- Provide operational monitoring through Slack alerts
- Handle API failures with retry and fallback logic

### **Problem**
Lead submission systems often generate duplicate events due to:
- repeated form submissions
- webhook retries
- API timeouts
- user refreshes or accidental resubmits

Without protection, this can result in:
- duplicate CRM updates
- repeated Slack notifications
- unnecessary API calls
- inconsistent lead state

This workflow ensures that each lead is processed exactly once, even when the trigger fires multiple times.

### **Architecture**

GoHighLevel Lead Submission
            │
            ▼
Webhook Trigger (New Lead Inbound)
            │
            ▼
Budget Qualification Filter (>$5k)
      ┌───────────────┴───────────────┐
      ▼                               ▼
Filtered Lead                     Qualified Lead
(Slack Notification)                   │
                                       ▼
                           Fetch Contact Metadata
                             (GHL API Request)
                                       │
                                       ▼
                           Idempotency Tag Check
                        (Has "VIP Processed" Tag?)
                           ┌───────────┴───────────┐
                           ▼                       ▼
                     Duplicate Lead            New Lead
                     Slack Notification            │
                                                   ▼
                                       Update Contact (Add VIP Tag)
                                                   │
                                                   ▼
                                        Update Verification Gate
                           ┌──────────────┴──────────────┐
                           ▼                             ▼
                    VIP Lead Provisioned         Update Failed
                      Slack Notification        Critical Slack Alert

### **Global Error Monitoring**

Workflow Execution Failure
            │
            ▼
n8n Error Trigger
            │
            ▼
Slack Technical Alert

### **Tech Stack**
- GoHighLevel – CRM platform and webhook source
- n8n – workflow orchestration
- GoHighLevel REST API – contact retrieval and updates
- Slack – operational alerting and monitoring
