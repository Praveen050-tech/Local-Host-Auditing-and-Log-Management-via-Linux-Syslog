Local Host Auditing and Log Management via Linux Syslog

1. Project Overview
  This project focuses on the foundational mechanics of host-level visibility, internal access auditing, and authentication log tracking. Utilizing the native Linux logging infrastructure, this lab configures a real-time tracking pipeline to monitor the security auditing trail (auth.log). The objective is to programmatically isolate failed authentication anomalies and successful administrative privilege escalations (sudo tracking), creating a verifiable forensic audit trail of local user activity.

2. Technical Architecture & Data Flow
The logging pipeline tracks local execution contexts and authentication subsystems in real-time:

      [ User Shell Activity ] ──> [ Pluggable Authentication Modules (PAM) ] ──> [ Syslog /var/log/auth.log ]
                                                                                         │
                                                                                         v
      [ Standardized SOC Alert ] <── [ Target Pattern Match (FAILED/SUDO) ] <── [ Stateful Stream Monitor ]

3. Deployment & Implementation Steps
Step 1: Environment Initialization
Provision a clean, isolated project workspace within your Kali Linux terminal to manage the host auditing logs:

Bash

    mkdir -p ~/portfolio_labs/host_auditing
    cd ~/portfolio_labs/host_auditing

Step 2: Live Stream Monitoring
Initialize a live, tail-mode monitoring engine targeting the central system authentication trail to capture telemetry dynamically:

Bash

    sudo tail -f /var/log/auth.log

<img width="1919" height="371" alt="Screenshot 2026-06-28 145612" src="https://github.com/user-attachments/assets/d4431d2c-a032-47c0-a22b-8b781633970a" />

Step 3: Local Telemetry Generation
In a separate parallel terminal interface, generate deliberate authentication milestones to test engine verification:

Bash

    # 1. Simulate an unauthorized administrative privilege switch
    su root
    
    # 2. Execute a privileged directory listing via the authorization wrapper
    sudo ls /root

<img width="1919" height="436" alt="Screenshot 2026-06-28 145631" src="https://github.com/user-attachments/assets/d56270b1-86c8-4f01-a5ab-20f594904e71" />

4. Operational Forensic Verification Logs
The monitoring stream accurately recorded the target authentication states in standard plaintext format:

Plaintext

    2026-06-28T05:22:25.711229-04:00 kali su[39460]: FAILED SU (to root) kali on pts/2
    2026-06-28T05:23:08.394139-04:00 kali sudo:      kali : TTY=pts/2 ; PWD=/home/kali ; USER=root ; COMMAND=/usr/bin/ls /root
    2026-06-28T05:23:08.396067-04:00 kali sudo: pam_unix(sudo:session): session opened for user root(uid=0) by kali(uid=1000)
    2026-06-28T05:23:08.411055-04:00 kali sudo: pam_unix(sudo:session): session closed for user root

Analytical Forensic Breakdown:

  FAILED SU (to root): Flags a high-priority authentication anomaly representing potential local credential guessing or unauthorized horizontal movement.
  
  USER=root ; COMMAND=/usr/bin/ls /root: Logs the explicit binary execution path run under administrative authority, maintaining non-repudiation.
  
  uid=1000 to uid=0: Tracks the precise identity transition from the low-privilege standard context (kali) into the root execution space.

<img width="1919" height="978" alt="Screenshot 2026-06-28 145546" src="https://github.com/user-attachments/assets/1cd22b63-baaa-4171-93d9-94d36452db82" />

5. Key Cybersecurity Takeaways

Internal Threat Visibility: Network security mechanisms cannot catch malicious behavior occurring entirely inside a compromised host. Local authentication logging is essential for detecting inside threats and compromised credentials.

Non-Repudiation Architecture: By tracking the original uid (1000) switching to the root context, the system ensures accountability, preventing users from hiding behind generic administrative accounts.

Continuous Monitoring over Static Analysis: Transitioning from reactive log parsing to real-time stream inspection (tail -f) enables automated security architectures to identify and intercept local brute-force attempts instantly.
