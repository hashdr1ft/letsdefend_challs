# GOOGLE CLOUD COMPROMISE


**CHALLENGE :  https://app.letsdefend.io/challenge/google-cloud-compromise**

## Overview

This writeup documents the forensic analysis of Google Cloud audit logs from a compromised project. The investigation involved examining JSON-formatted audit logs to extract key indicators of compromise and attack methodology.

## Analysis Questions and Findings

### Question 1: Project Identification

**What's the name of the project that was compromised?**

The project name can be identified from the `project_id` field present throughout the audit logs:

```json
"labels": {
  "project_id": "cryptostartup"
}
```

**Answer: cryptostartup**

### Question 2: Compromised Identity

**What google cloud identity is compromised?**

The compromised service account is found in the `authenticationInfo` section of each log entry:

```json
"authenticationInfo": {
  "principalEmail": "cloud-storage-helper@cryptostartup.iam.gserviceaccount.com"
}

```

**Answer: cloud-storage-helper@cryptostartup.iam.gserviceaccount.com**

### Question 3: Source IP Address

**What IP address is the identity authenticated from?**

The source IP is consistently recorded in the `requestMetadata` section:

```json
"requestMetadata": {
  "callerIp": "178.132.108.38"
}

```

**Answer: 178.132.108.38**

### Question 4: Geographic Origin

**What country does the IP originate from?**

src : https://whatismyipaddress.com/ip/178.132.108.38

THE IP BELONGS TO  **`ROMANIA`**

<img width="841" height="523" alt="Screenshot 2025-08-19 at 1 27 27 PM" src="https://github.com/user-attachments/assets/90337f73-c92d-48f4-a251-76349810d109" />


**Answer: Romania**

### Question 5: Attack Platform

**What device did the attack come from?**

The device information is extracted from the user agent string in the logs:

```json
"callerSuppliedUserAgent": "google-cloud-sdk gcloud/390.0.0 command/gcloud.projects.get-iam-policy invocation-id/a4713040dd8e4655acb6c5b4e465f403 environment/None environment-version/None interactive/True from-script/False python/3.11.3 term/xterm-256color (Macintosh; Intel Mac OS X 22.5.0)"

```

**Answer: Macintosh**

### Question 6: Initial Failed API Call

**What was the first failed API call made by this identity?**

Examining the timestamps, the earliest failed call occurred at `2023-07-27T00:16:24.221685Z`:

```json
"methodName": "google.api.serviceusage.v1.ServiceUsage.EnableService",
"timestamp": "2023-07-27T00:16:24.221685Z",
"status": {
  "code": 7,
  "message": "Permission denied to enable service [compute.googleapis.com]"
}
```

**Answer: google.api.serviceusage.v1.ServiceUsage.EnableService**

### Question 7: Enumerated Storage Bucket

**What storage bucket was enumerated?**

The bucket enumeration activity shows access to a specific bucket:

```json
"authorizationInfo": [
  {
    "resource": "projects/_/buckets/importantbucket",
    "permission": "storage.objects.list",
    "granted": true
  }
]
```

**Answer: importantbucket**

### Question 8: Data Exfiltration API Call

**What API call was used to exfiltrate an item from this bucket?**

The successful data access operation used:

```json
"methodName": "storage.objects.get",
"authorizationInfo": [
  {
    "resource": "projects/_/buckets/importantbucket/objects/secretcode.java",
    "permission": "storage.objects.get",
    "granted": true
  }
]
```

**Answer: storage.objects.get**

### Question 9: Command-Line Tool Usage

**Which Google Cloud command-line tool was used during the exfiltration attempt?**

The user agent string reveals the tool used for file operations:

```json
"callerSuppliedUserAgent": "apitools Python/3.11.3 gsutil/5.10 (darwin) analytics/disabled interactive/True command/cp google-cloud-sdk/390.0.0"
```

**Answer: gsutil**

### Question 10: Exfiltrated File

**What is the name of the file that was exfiltrated from the storage bucket?**

The specific file accessed is identified in the resource name:

```json
"resourceName": "projects/_/buckets/importantbucket/objects/secretcode.java"
```

**Answer: secretcode.java**

**Do give this repository a star .**  **`Happy Hacking`**

**Hashdr1ft**
