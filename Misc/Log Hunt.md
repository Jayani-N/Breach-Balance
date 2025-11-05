# Log Hunt (150 pts)

## Desc:

    Find the suspicious IP(s) and submit the flag in the exact format shown below.
    
    format :  "bab{ip_<followed by the correct ip>}"

## Solution:

    1.2.3.4 — this one here is highly suspicious (admin token attempts, payload detected, SQL errors, rate-limit exceeded).
    
    log lines:
    
    [2025-10-25 07:00:13] WARN  1.2.3.4 Suspicious file access filename=/var/log/a.log uid=1001
    [2025-10-29 02:13:01] ERROR 1.2.3.4 Admin token attempt endpoint=/admin/login uid=0
    [2025-10-29 02:16:33] ERROR 1.2.3.4 Payload detected endpoint=/upload uid=1002



## Flag

    bab{ip_1.2.3.4}
