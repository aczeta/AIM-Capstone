# Training a Supervised Model For Cybersecurity Purposes

## By Allan Zeta

## Problem Statement

Company A owns a website used to allow direct online shopping for its products. For users to shop on the platform, they must have an account with personal data provided for KYC purposes and for using data science to analyze customer behaviour.

There are three forms of attacks Company A is interested in detecting: Dictionary Attack, Distributed Denial of Service(DDoS) and Vulnerabiltiy Exploits. Dictionary Attacks consist of an attacker trying to guess login credentials of the users. DDoS works by an attacker overloading the servers they are attack by sending data beyond the server's ability to handle. Vulnerability exploits work by using vulnerabilities in software used the servers to allow arbitrary execution of malicious code.

If there is an attack, the customer's private data may be leaked and be used for identity theft which causes harm to customers. Furthermore, certain attacks can render the service unavailable to customers while ramping up hosting costs.  As such, Company A requires a system that would detect breaches via machine learning.

From a technical perspective, we are interested if a session is either an attack or if it is a normal session. This allows us to treat the problem as a binary classification problem. Since a lot of possible attack vectors are well known , we can use an appropriate dataset with supervised learning to detect attack with known methods. Once an attack has been detected, the system then notifies the customer via email and the session cancelled before freezing the account.  To measure success, four metrics will be used accuracy, precision recall and F1-score.

- Accuracy: describes how many attacks or normals the model correctly guessed
- Precision: describes how many attacks were correctly guessed
- Recall: describes how many attacks wre found compared to all attacks in the testing set.
- F1-score: combines precision and recall into one metric. High F1 score means both precision and recall are high

From the business perspective, detecting a possible breach prevents the company form incurring costs due to a data breach such as but not limited to fines, higher insurance premiums and third-party audits. Furthermore, since the company uses a Pay-As-You-Go plan for hosting where the company only pay for compute used, a Distributed Denial of Service attack can cause costs to ramp up beyond expectations causing financial strain.

## Dataset Overview and Dictionary

The dataset to be used is Samudrala's Cybersecurity Detection Dataset found in [Kaggle](https://www.kaggle.com/datasets/dnkumars/cybersecurity-intrusion-detection-dataset)
A diagram of a portion of the dataset will be provided below for overview:
 ![data_left](../images/data_left.png) ![data_right](../images/data_right.png)
The aforementioned dataset provides a minimum of 9000 records from the perspective of the defender.

IP reputation score is used instead of IP for prototyping since IP addresses would need to be analyzed for data pertaining to geographic location and which ISP own the network block it belongs to. (Note: IP Addresses are allocated to ISPs in bulk via network block which then allocates them to client based on needs.) Geographic data and ISP ownership can be found in bulk for 9000 requires paid services currently accessible to the author. There may be free services, however they come with severe limits( 45-60 IP/min ).

To explain the columns and the possible values they contain, we have provided a data dictionary below.

### Data Dictionary

- session_id: an alphanumeric ID used to uniquely identify a session. Denoted in th form SID-XXXXX where X is a number
- network_packet_size: size of network packets in bytes. Ranges from 64-1500 bytes. Smaller packets may transfer control messages while larger packets transfer bulk data. Packets exceeding 1500 bytes are broken up. An attacker may hide malicious code among split up packets.
- protocol_type: protocol used in sending packets. There are three values: TCP, UDP and ICMP
  - TCP: stands for Transmission Contol Protocol. Used for secure reliable connections. Typically used for HTTP/s and SSH.
  - UPD: stands for User Datagram Protocol. Typically used for media.
  - ICMP: stands for Internet Control Message Protocol. Used for network diagnostics. Favored for Distributed Denial Attacks.

- encryption_used: what encryption is used. Has three possible values: AES, DES, None
  - AES: stands for Advanced Encryption Standard. Common and Highly secure
  - DES: stands for Data Enctyption Standard. Less Secure standard superseded by AES
  - None: indicate unencrypted transmissions.

No or weak encryption indicates a possible attack since it allows vulnerability exploits.

- login_attempts: integer value denoting number of login attempts. High value indicates a possible dictionary attacks while low value (1-3) indicate normal usage.
- session_duration: measured in seconds. High values may indicate an attacker trying to prolong infiltration.
- failed_logins: how many login attempts failed. Multiple failed login attempts follwed by a sucessful login indicate a possible breached account.
- unusual time_access: indicates if login is on business hours. Attackers may try to avoid buisness hours to avoid detection. 
  - Assumed to be generated by Company A's system frim access time for ease of processing

- ip_reputation_score: on a scale from 0 to 1 indicates likelihood of being used for attacks of an ip address. Higher values indicate an IP used previously for known attacks
  - Assumed to be provided by Company A's threat intelligence provider
- browser_type: what web browser is used by the user. Has the following values: Edge, Safari, Firefox, Chrome and Unknown.
  - Unknown browsers may indicate attack.

### Data Overview

The dataset is synthetically generated with low dimensionality which allows model prototyping on computationally constrained devices. Based on the EDA notebook, there are no missing or duplicate entries. To understand data we perform both univariate and multivariate analysis.

#### Univariate Analysis

First, we analyze the characteristics of each variable and what it may imply.
![summary_left](../images/summary_left.png)