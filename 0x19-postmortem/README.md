 Postmortem: Nginx Server Outage on August 20, 2024
 Issue Summary

Duration: 8:30 AM - 11:15 AM WAT (2 hours 45 minutes)  
Impact: The primary web application experienced significant slowdowns and intermittent service outages, affecting approximately 75% of users. Users reported slow page loads, frequent timeouts, and sporadic 502 Bad Gateway errors.  
Root Cause: A misconfiguration in the ULIMIT settings for the Nginx server, which restricted the maximum number of file descriptors, led to resource exhaustion during a traffic surge.

 Timeline

- 08:30 AM: Issue detected by a monitoring alert indicating high latency and an increase in 502 errors.
- 08:35 AM: DevOps engineer noticed significant spikes in server load and began investigating Nginx performance.
- 08:45 AM: Initial assumption: a possible DDoS attack or unusual traffic pattern. Traffic was analyzed, but no anomalies were detected.
- 09:00 AM: Database performance was checked due to suspicion of slow queries, but no issues were found.
- 09:15 AM: Investigation into server resources revealed high file descriptor usage, pointing towards Nginx configuration.
- 09:30 AM: The incident was escalated to the lead DevOps engineer for deeper analysis.
- 09:45 AM: Further analysis confirmed that the ULIMIT was set too low, causing Nginx to exhaust available file descriptors.
- 10:00 AM: Decision was made to increase the ULIMIT setting and restart the Nginx service.
- 10:30 AM: Nginx was restarted, but slow recovery observed due to high incoming traffic.
- 11:00 AM: Load balancers were adjusted to gradually route traffic back to the server.
- 11:15 AM: Full service was restored, and normal operation resumed.

 Root Cause and Resolution

Root Cause: 
The outage was caused by an insufficient ULIMIT configuration on the Nginx server. The ULIMIT, which determines the maximum number of file descriptors that a process can open, was set to 1024—a value inadequate for handling the incoming traffic surge. This caused Nginx to rapidly exhaust its available file descriptors, leading to failed connections, 502 errors, and eventual server crashes.

Resolution:  
The fix involved increasing the ULIMIT setting from 1024 to 65536, which allowed Nginx to handle a significantly larger number of simultaneous connections. After adjusting the ULIMIT, the Nginx service was restarted. To ensure a smooth recovery, load balancers were reconfigured to gradually reintroduce traffic to the server, avoiding further overloading. The system was closely monitored during the recovery phase to ensure stability.

Corrective and Preventative Measures

Improvements/Fixes:
- Capacity Planning: Regularly review and adjust server configuration settings (e.g., ULIMIT) in anticipation of traffic spikes, especially before major events or campaigns.
- Enhanced Monitoring: Implement more granular monitoring of file descriptor usage and set up automated alerts for when usage approaches critical limits.
- Load Testing: Conduct load tests under high-traffic scenarios to ensure that all configurations can handle peak loads without failure.

Task List:
1. Increase ULIMIT: Permanently adjust the ULIMIT setting on all production servers to accommodate high traffic volumes.
2. Deploy Monitoring Tools: Add monitoring specifically for file descriptor usage in Nginx and other critical services.
3. Document Configuration: Update the server setup and deployment documentation to include best practices for configuring ULIMIT and other critical system limits.
4. Perform Regular Load Testing: Schedule and execute regular load tests, particularly before large-scale deployments or marketing campaigns, to validate server configurations.
5. Review Incident Response: Conduct a review of the incident response process to identify any delays or inefficiencies and implement improvements.

This incident underscored the importance of proactive server configuration management and the need for robust monitoring systems to prevent similar outages in the future.

