## AIM Capstone
# Problem Statement
Company A owns a website used to allow direct online shopping for its products. For users to shop on the platform, they must have an account with personal data provided for KYC purposes and for using data science to analyze customer behaviour. If there is an intrusion, the customer's private data may be leaked and be used for identity theft which causes harm to customers. As such, Company A requires a system that would detect breaches via machine learning.
TODO : CITe the dataset
From a technical perspective, since the dataset to be used has the attacks labelled, it is a binary classification problem since there can only be two results of interest: attack or normal. Once an attack has been detected, the system then notifies the customer via email and the session cancelled before freezing the account.  To measure success, four metrics will be used accuracy, precision recall and F1-score. 
- Accuracy: describes how many attacks or normals the model correctly guessed
- Precision: describes how many attacks were correctly guessed
- Recall: describes how many attacks wre found compared to all attacks in the testing set.
- F1-score: combines precision and recall into one metric. High F1 score means both precision and recall are high
From the business perspective, detecting a possible breach prevents the company form incurring costs due to a data breach such as but not limited to fines, higher insurance premiums and third-party audits.