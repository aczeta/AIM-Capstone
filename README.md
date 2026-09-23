## AIM Capstone
# Problem Statement
Company A owns a website used to allow direct online shopping for its products. For users to shop on the platform, they must have an account with personal data provided for KYC purposes and for using data science to analyze customer behaviour. If there is an intrusion, the customer's private data may be leaked and be used for identity theft which causes harm to customers. As such, Company A requires a system that would detect breaches via machine learning.

From a technical perspective, we are interested if a session is either an attack or if it is a normal session. This allows us to treat the problem as a binary classification problem. Since, a lot of possible attack vectors are well known, we can use an appropriate dataset with supervised learning to detect attack with known methods. Once an attack has been detected, the system then notifies the customer via email and the session cancelled before freezing the account.  To measure success, four metrics will be used accuracy, precision recall and F1-score. 
- Accuracy: describes how many attacks or normals the model correctly guessed
- Precision: describes how many attacks were correctly guessed
- Recall: describes how many attacks wre found compared to all attacks in the testing set.
- F1-score: combines precision and recall into one metric. High F1 score means both precision and recall are high

From the business perspective, detecting a possible breach prevents the company form incurring costs due to a data breach such as but not limited to fines, higher insurance premiums and third-party audits.