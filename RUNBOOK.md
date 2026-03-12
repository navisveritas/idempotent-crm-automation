# Runbook: Idempotent CRM Automation

### **System Health Monitoring**
* **Active Monitoring**: All technical failures are routed to the `#ops-alerts` Slack channel via the Global Error Trigger.
* **Retries**: HTTP nodes use exponential backoff. If a node stays red after 3 retries, manual investigation is required.

### **Incident Response**
#### **Scenario A: "Manual Action Required" Alert**
* **Cause**: The lead was qualified as a VIP, but the API failed to apply the tag in GHL.
* **Action**: 
    1. Open GoHighLevel.
    2. Search for the Contact ID provided in the Slack alert.
    3. Manually add the tag `vip-lead`.
    4. Move the lead to the "VIP Pipeline" stage.

#### **Scenario B: Webhook 404/Timeout**
* **Cause**: n8n service is down or GHL Webhook URL has changed.
* **Action**: Check n8n instance status and verify the Webhook URL in GHL Settings -> Workflows.