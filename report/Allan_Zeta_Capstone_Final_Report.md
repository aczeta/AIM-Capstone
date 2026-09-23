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
![data_left](../images/data_left.png)
![data_right](../images/data_right.png)
The aforementioned dataset provides a minimum of 9000 records from the perspective of the defender.

IP reputation score is used instead of IP for prototyping since IP addresses would need to be analyzed for data pertaining to geographic location and which ISP own the network block it belongs to. (Note: IP Addresses are allocated to ISPs in bulk via network block which then allocates them to client based on needs.) Geographic data and ISP ownership can be found in bulk for 9000 requires paid services currently accessible to the author. There may be free services, however they come with severe limits( 45-60 IP/min ).

To explain the columns and the possible values they contain, we have provided a data dictionary below.

### Data Dictionary

- session_id: an alphanumeric ID used to uniquely identify a session. Denoted in th form SID-XXXXX where X is a number
- network_packet_size: size of network packets in bytes. Ranges from 64-1500 bytes. Smaller packets may transfer control messages while larger packets transfer bulk data. Packets exceeding 1500 bytes are broken up. An attacker may hide malicious code among split up packets. Furthermore, packet size reaching or exceeding this limit is used in DDoS attacks
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
![summary_right](../images/summary_right.png)
It can be seen that the mean of login attempts is higher than what is considered normal. This may indicate a high presence of dictionary attacks. It should be noted that network packet size never exceeded or reached 1500 which may indicate a low likelihood of DDoS attacks in the dataset.

Looking at session duration, we have an average of 729 seconds or 20 minutes which is reasonable time for online shopping in the platform. This may indicate that detected attacks are not visible based solely on session duration.

Moving on the the characteristics of the categorical variables, we have:
![protocol](../images/protocol.png)

It can be seen that TCP dominates the dataset. This implies that most connections are regular browsing connections (i.e. online shopping).

![encryption](../images/encryption.png)
It can be seen that no encryption is 20% and DES is 30% which indicates that half of the userbase is using unsecured connections.

![browser](../images/browser.png)
At least half of the user base uses Chrome which is to be expected since Chrome is the dominant browser. [(StatsCounter, 2026)](https://gs.statcounter.com/) Furthermore, Firefox and Edge have higher than expected utilization which means that the website has strong appeal to Firefox's and Edge's userbase.

![attack](../images/attack.png)
It can be seen that attack and normal seesion are well-balanced which reduces the need for dealing with class-imbalances,

#### Multivariate Analysis
We begin with a heatmap of the correllation matrix of all numerical variables along with attack_detected.
![heatmap](../images/corr_heatmap.png)

It is immediately obvious that the various features have minimal correlation with each other. This means we have minimal issues with collinearity harming model performance and training time. Furthermore, it can be seen that ip_reputation_score, failed_logins and login_attempts are correlated with the target. It is therefore expected that they will be important features.
Finally, we have a pair plot.
![pairplot](../images/pairplot.png)
Based on the pair plot, we have some radial clustering around ip_reputation_score(for scores greater than 0.6 ) As such, a Support Vector Machine with a radial kernel may be the best model.

## Data Scaling and Feature Selection
Before feature selection, we must first scale the data. This was done by making separate SkLearn Pipeline Objects for scaling numerical data and for encoding categorical data. The objects are then used to make a ColumnTransformer for easy and precise scaling and encoding of data. After scaling and encoding, feature selection is done by filtering based on Variance Treshold. This is done to remove numerical variables that do not change much and thus have minimal predictive power. It will also remove categorical variables that rarely happen. Since the classes are well-balanced, they should have minimal predictive power. This causes Safari, Unknown browser along with ICMP protocol be dropped due to rarity.

## Model Training and Selection
Four supervised models with different approaches are evaluated as candidates for best estimator. The models are Logistic Regression, Random Forest Classifier, Support Vector Machines and Multi-Layer Perceptron Classifier Neural Network. To choose the best model, hyper-parameter tuning is done across the multiple models with f1-scoring as the primary evaluation criteria. F1-scoring is the primary metric for evaluation because it minimizes false negatives and false positives. False negatives mean an attack got through and false positive means a legitimate user was treated as an attacker which means they got falsely booted out of the session and their accounts locked. Needless to say, this is a major inconvenience to the customer.

Random search instead of grid search is used for hyperparameter tuning due to computational constraints. 

The tuning selected a Support Vector Machine model with radial kernel as expected earlier in the EDA. The metrics are provided below:
![metrics](../images/metrics.png)
It can be seen that the model has remarkable performance with regards to F1 scoring. The model also has very high precision which means that that false positives are very rare. Meanwhile, it has decent recall which means that false negatives may occasionally occur.

## Bias and Fairness Analysis

To assess model bias and limitation, we must first use SHAP to determine feature importance. Using it gave the SHAP values for the selected model which can be found below:
![shap](../images/shap_summary.png)

It is evident that the most important features are failed logins, login attempts and IP reputation score. This means a heavy bias towards those features. Attacks that are not Dictionary Attacks or uses an IP with good reputation score can be treated as a false negative by the model. Furthermore, sessions with users that made multiple errors inputting their passwords due to legitimate reasons(forgetting them or mispressing key in their keyboards) or who got assigned an IP with high score by their ISP are likely to be consider a false postive by their model.

With regards to fairness, the dataset used for training the model has no entries regarding gender, race age or socioeconmic impact. Hence, there is no expected bias against these sensitive groups.

To mitigate these limitations in future development cycles, it is recommended that average times between login attempts be recorded and included since attackers amy likely be using automated tools that result in extremely short times compared to a legitimate user who made a mistake in logging in. 

It is also recommended to use Multi-Factor Authentication. This would result in a field in the future dataset if the session used a corresponding additional authentication method(e.g. Passkeys, TOTP, Email Codes) for login. This is expected to reduce the likelihood of users making mistakes while logging in via passwordless login enabled by additional authenitcation. This also mitigates issues due to IP reputation scores with additional authentication providing another data point that a user is not an attacker.

Since the model has bias towards features tied to Dictionary Attacks, it is recommended to use hyperparameter tuning to determine the best multi-class classification model which once identifed will be used as the basis for a subsequent pipeline that will apply SHAP for feature selection with the selected model. This pipeline will then result to a lighter, multi-class model fit for production use.

Finally, to mitigate the limitations of the model towards novel forms of attack not attested in the labelled dataset, it is recommended to train an unsupervised model that can be used to catch unknown threats that got through the supervised model.