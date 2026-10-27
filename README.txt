IR-2026-001 — SIMULATED LOG PACK
=================================

Purpose
-------
Training data for the fictional case "Suspected Account Compromise and Privilege Abuse".
All records in this folder are fabricated for study and do not come from a real system.

Files
-----
1. authentication.log
   Human-readable authentication log: 12 failed logins followed by a successful login.

2. cloudtrail_events.jsonl
   Two JSON-lines records: an illustrative IAM PutUserPolicy event and an S3 GetObject event.
   Timestamps are in UTC (Z). Convert to India Standard Time (UTC+05:30) to compare with
   authentication.log and data_access.log.jsonl.

3. data_access.log.jsonl
   A normalized-style object read event with a local +05:30 timestamp.

4. normalized_events.csv
   Simplified common schema for quick searches, timeline building, and SIEM practice.

Scenario timeline (India Standard Time, UTC+05:30)
--------------------------------------------------
09:10–09:15  12 failed logins for employee01 from 203.0.113.25
09:16        Successful login from the same unfamiliar source; MFA status unknown
09:19        IAM policy change associated with the account/session
09:22        Sensitive object access recorded

Important interpretation limits
--------------------------------
- 203.0.113.25 is an example/documentation IP address, not a real attacker address.
- Account names, account ID, role names, bucket, object, session, event IDs, and request IDs are synthetic.
- The scenario says the IAM policy change was later confirmed unauthorised; the logs alone do not
  show the approval workflow, so in a real investigation you would verify change records and ownership.
- The dataset shows a data-access event. It does NOT prove data exfiltration.
- MFA status is intentionally unknown. Do not claim MFA was bypassed.
- The AWS CloudTrail event times are UTC; authentication/data-access records use India Standard Time.
- These are training examples, not exact production logs or a complete forensic dataset.

Suggested practice tasks
------------------------
1. Count failed logins by user and source IP.
2. Identify the successful login following the failures.
3. Build a timeline by converting UTC to UTC+05:30.
4. Correlate user/session/source IP across the three sources.
5. Explain why the IAM change raises severity.
6. State what is confirmed, what is only suspicious, and what remains unknown.
7. Write a cautious conclusion that does not assert exfiltration without evidence.

Splunk starting searches (field names depend on how you import the files)
-------------------------------------------------------------------------
index=ir2026 user="employee01" | stats count by source event_type result

index=ir2026 source_ip="203.0.113.25" | sort 0 timestamp_local

index=ir2026 user="employee01" | transaction user maxspan=15m

ELK/Kibana practice
-------------------
Import normalized_events.csv into a test index/data view, map timestamp_local as a date field,
then filter user: employee01 and source_ip: 203.0.113.25. Check the time zone setting when
comparing with the JSONL CloudTrail records.
