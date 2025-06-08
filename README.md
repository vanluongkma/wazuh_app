# Wazuh Custom Dashboards for Splunk

This Splunk app provides a collection of pre-built dashboards to visualize Wazuh data in different security domains such as incident response, malware detection, vulnerabilities, and more.

## Included Dashboards

The following dashboards are included in this app:

- **wazuh-amazon-aws**  
  Monitor Wazuh integration with AWS CloudTrail, GuardDuty, and more.

- **wazuh-docker-listener**  
  View Docker container events collected by Wazuh agents.

- **wazuh-incident-response**  
  Track and analyze security incidents using Wazuh event data.

- **wazuh-malware-detection**  
  Visualize malware-related events and file integrity monitoring alerts.

- **wazuh-pci-dss**  
  Compliance-focused dashboard mapped to PCI DSS requirements.

- **wazuh-security-events**  
  General security events overview across your infrastructure.

- **wazuh-vulnerabilities**  
  Detect and analyze vulnerability-related logs and CVEs.

## Requirements

- Splunk Enterprise or Splunk Cloud
- Compatible with Splunk 8.x and 9.x

## Installation
### Installing Splunk forwarder on the Wazuh server
The Splunk forwarder must stream the data from the Wazuh server to the Splunk indexes created previously.

Follow the Splunk documentation to [install the Splunk universal forwarder](https://docs.splunk.com/Documentation/Forwarder/9.0.4/Forwarder/Installanixuniversalforwarder#Install_the_universal_forwarder_on_Linux) on the Wazuh Server.

### Configuring the Splunk forwarder
1. Set the following configuration in ``/opt/splunkforwarder/etc/system/local/inputs.conf`` file. This configures the Splunk forwarder to monitor the Wazuh ``/var/ossec/logs/alerts/alerts.json`` alerts file. Where ``<WAZUH_SERVER_HOST>`` is a name of your choice.

    ``` bash
    # Example
    [monitor:///var/ossec/logs/alerts/alerts.json]
    sourcetype = wazuh:alerts
    index = wazuh-alerts

    [monitor:///var/ossec/logs/archives/archives.json]
    sourcetype = wazuh:archives
    index = wazuh-alerts

    [monitor:///var/ossec/logs/ossec.log]
    sourcetype = wazuh:ossec
    index = wazuh-alerts

    [monitor:///var/ossec/logs/api/api.log]
    sourcetype = wazuh:api
    index = wazuh-alerts

    [monitor:///var/ossec/logs/active-responses.log]
    sourcetype = wazuh:response
    index = wazuh-alerts

    disabled = 0
    host = <WAZUH_SERVER_HOST>
    ```
2. Set the following configuration in the ``/opt/splunkforwarder/etc/system/local/props.conf`` file to parse the data forwarded to Splunk:
    ```bash
    [wazuh-alerts]
    DATETIME_CONFIG =
    INDEXED_EXTRACTIONS = json
    KV_MODE = none
    NO_BINARY_CHECK = true
    category = Application
    disabled = false
    pulldown_type = true
    ```

3. Set the following configuration in the ``/opt/splunkforwarder/etc/system/local/outputs.conf`` file to define how the alerts are forwarded to Splunk. Where ``<SPLUNK_INDEXER_ADDRESS>`` is your Splunk server IP address. For Cloud instances, the Splunk indexer address is the cloud instance address.
    ```bash
    defaultGroup = default-autolb-group

    [tcpout:default-autolb-group]
    server = <SPLUNK_INDEXER_ADDRESS>:9997

    [tcpout-server://<SPLUNK_INDEXER_ADDRESS>:9997]
    ```

### Running the forwarder
1. [Start the Splunk Forwarder](https://docs.splunk.com/Documentation/Forwarder/latest/Forwarder/StartorStoptheuniversalforwarder#Start_the_universal_forwarder) following Splunk documentation.

2. Run the following command to verify the connection is established:
    ```bash
    sudo /opt/splunkforwarder/bin/splunk list forward-server
    ```

### Install app on Splunk
1. Package this app folder into a `.tar.gz` file:
   ```bash
   tar -czvf wazuh_app.tar.gz wazuh_app/
   ```
2. In Splunk Web:

- Go to ``Apps`` > ``Manage Apps``

- Click ``Install app from file``

- Upload ``wazuh_app.tar.gz``

- Restart Splunk if required

3. Go to the Wazuh Dashboards app to explore the dashboards.
![gif](/static/wazuh_dashboards_app.gif)