---
title: "How to use Virus Total API for check PE file"
classes: wide
search: false
categories: 
  - Information Security
  - Malware Analysis
toc: true
toc_label: "Contents"
date: 2018-09-21 17:47:00
---
### Details:
PE file : putty.exe

### Source Code
```python
import requests
url = 'https://www.virustotal.com/vtapi/v2/file/scan'
params = {'apikey':'<your-api>'}
files = {'file':('/home/wirl-1424/python/sample/putty.exe', open('/home/wirl-1424/python/sample/putty.exe', 'rb'))}
response = requests.post(url, files=files, params=params)
print(response.json())
```

### output
```json
{'scan_id': '7afb56dd48565c3c9804f683c80ef47e5333f847f2d3211ec11ed13ad36061e1-1537521908', 'sha1': '3b1333f826e5fe36395042fe0f1b895f4a373f1b', 'resource': '7afb56dd48565c3c9804f683c80ef47e5333f847f2d3211ec11ed13ad36061e1', 'response_code': 1, 'sha256': '7afb56dd48565c3c9804f683c80ef47e5333f847f2d3211ec11ed13ad36061e1', 'permalink': 'https://www.virustotal.com/file/7afb56dd48565c3c9804f683c80ef47e5333f847f2d3211ec11ed13ad36061e1/analysis/1537521908/', 'md5': '54cb91395cdaad9d47882533c21fc0e9', 'verbose_msg': 'Scan request successfully queued, come back later for the report'}
```

### Source Code
```python
import requests
params = {'apikey':'<your-api>', 'resource':'7afb56dd48565c3c9804f683c80ef47e5333f847f2d3211ec11ed13ad36061e1'}
headers = {
    "Accept-Encoding": "gzip, deflate",
    "User-Agent" : "gzip,  My Python requests library example client or username"
}
response = requests.get('https://www.virustotal.com/vtapi/v2/file/report',
  params=params, headers=headers)
json_response = response.json()
print(json_response)
```

### output
```json
    {'scans': {'Bkav': {'detected': False, 'version': '1.3.0.8876', 'result': None, 'update': '20180919'}, 'MicroWorld-eScan': {'detected': False, 'version': '14.0.297.0', 'result': None, 'update': '20180920'}, 'CMC': {'detected': False, 'version': '1.1.0.977', 'result': None, 'update': '20180920'}, 'CAT-QuickHeal': {'detected': False, 'version': '14.00', 'result': None, 'update': '20180918'}, 'McAfee': {'detected': False, 'version': '6.0.6.653', 'result': None, 'update': '20180920'}, 'Cylance': {'detected': False, 'version': '2.3.1.101', 'result': None, 'update': '20180920'}, 'Zillya': {'detected': False, 'version': '2.0.0.3643', 'result': None, 'update': '20180920'}, 'TheHacker': {'detected': False, 'version': '6.8.0.5.3703', 'result': None, 'update': '20180920'}, 'BitDefender': {'detected': False, 'version': '7.2', 'result': None, 'update': '20180920'}, 'K7GW': {'detected': False, 'version': '11.4.28458', 'result': None, 'update': '20180920'}, 'K7AntiVirus': {'detected': False, 'version': '11.4.28459', 'result': None, 'update': '20180920'}, 'TrendMicro': {'detected': False, 'version': '10.0.0.1040', 'result': None, 'update': '20180920'}, 'Baidu': {'detected': False, 'version': '1.0.0.2', 'result': None, 'update': '20180914'}, 'Babable': {'detected': False, 'version': '9107201', 'result': None, 'update': '20180918'}, 'Cyren': {'detected': False, 'version': '6.0.0.4', 'result': None, 'update': '20180920'}, 'Symantec': {'detected': False, 'version': '1.7.0.0', 'result': None, 'update': '20180920'}, 'ESET-NOD32': {'detected': False, 'version': '18085', 'result': None, 'update': '20180920'}, 'TrendMicro-HouseCall': {'detected': False, 'version': '9.950.0.1006', 'result': None, 'update': '20180920'}, 'Paloalto': {'detected': False, 'version': '1.0', 'result': None, 'update': '20180920'}, 'ClamAV': {'detected': False, 'version': '0.100.1.0', 'result': None, 'update': '20180920'}, 'Kaspersky': {'detected': False, 'version': '15.0.1.13', 'result': None, 'update': '20180920'}, 'Alibaba': {'detected': False, 'version': '0.1.0.2', 'result': None, 'update': '20180912'}, 'NANO-Antivirus': {'detected': False, 'version': '1.0.126.23745', 'result': None, 'update': '20180920'}, 'ViRobot': {'detected': False, 'version': '2014.3.20.0', 'result': None, 'update': '20180920'}, 'SUPERAntiSpyware': {'detected': False, 'version': '5.6.0.1032', 'result': None, 'update': '20180907'}, 'Tencent': {'detected': False, 'version': '1.0.0.1', 'result': None, 'update': '20180920'}, 'Ad-Aware': {'detected': False, 'version': '3.0.5.370', 'result': None, 'update': '20180920'}, 'Sophos': {'detected': False, 'version': '4.98.0', 'result': None, 'update': '20180920'}, 'Comodo': {'detected': False, 'version': None, 'result': None, 'update': '20180920'}, 'F-Secure': {'detected': False, 'version': '11.0.19100.45', 'result': None, 'update': '20180920'}, 'DrWeb': {'detected': False, 'version': '7.0.33.6080', 'result': None, 'update': '20180920'}, 'VIPRE': {'detected': False, 'version': '69724', 'result': None, 'update': '20180920'}, 'Invincea': {'detected': False, 'version': '6.3.5.26121', 'result': None, 'update': '20180717'}, 'McAfee-GW-Edition': {'detected': False, 'version': 'v2017.3010', 'result': None, 'update': '20180920'}, 'Emsisoft': {'detected': False, 'version': '2018.4.0.1029', 'result': None, 'update': '20180920'}, 'Ikarus': {'detected': False, 'version': '0.1.5.2', 'result': None, 'update': '20180920'}, 'F-Prot': {'detected': False, 'version': '4.7.1.166', 'result': None, 'update': '20180920'}, 'Jiangmin': {'detected': False, 'version': '16.0.100', 'result': None, 'update': '20180920'}, 'Webroot': {'detected': False, 'version': '1.0.0.403', 'result': None, 'update': '20180920'}, 'Avira': {'detected': False, 'version': '8.3.3.6', 'result': None, 'update': '20180920'}, 'Antiy-AVL': {'detected': False, 'version': '3.0.0.1', 'result': None, 'update': '20180920'}, 'Kingsoft': {'detected': False, 'version': '2013.8.14.323', 'result': None, 'update': '20180920'}, 'Microsoft': {'detected': False, 'version': '1.1.15200.1', 'result': None, 'update': '20180920'}, 'Endgame': {'detected': False, 'version': '3.0.1', 'result': None, 'update': '20180730'}, 'Arcabit': {'detected': False, 'version': '1.0.0.833', 'result': None, 'update': '20180920'}, 'AegisLab': {'detected': False, 'version': '4.2', 'result': None, 'update': '20180920'}, 'ZoneAlarm': {'detected': False, 'version': '1.0', 'result': None, 'update': '20180920'}, 'Avast-Mobile': {'detected': False, 'version': '180920-00', 'result': None, 'update': '20180920'}, 'GData': {'detected': False, 'version': 'A:25.18589B:25.13261', 'result': None, 'update': '20180920'}, 'TACHYON': {'detected': False, 'version': '2018-09-20.02', 'result': None, 'update': '20180920'}, 'AhnLab-V3': {'detected': False, 'version': '3.13.1.21616', 'result': None, 'update': '20180920'}, 'ALYac': {'detected': False, 'version': '1.1.1.5', 'result': None, 'update': '20180920'}, 'AVware': {'detected': False, 'version': '1.6.0.52', 'result': None, 'update': '20180920'}, 'MAX': {'detected': False, 'version': '2018.9.12.1', 'result': None, 'update': '20180920'}, 'VBA32': {'detected': False, 'version': '3.33.0', 'result': None, 'update': '20180920'}, 'Malwarebytes': {'detected': False, 'version': '2.1.1.1115', 'result': None, 'update': '20180920'}, 'Panda': {'detected': False, 'version': '4.6.4.2', 'result': None, 'update': '20180920'}, 'Zoner': {'detected': False, 'version': '1.0', 'result': None, 'update': '20180919'}, 'Rising': {'detected': False, 'version': '25.0.0.24', 'result': None, 'update': '20180920'}, 'Yandex': {'detected': True, 'version': '5.5.1.3', 'result': 'Trojan.Shelma!', 'update': '20180920'}, 'SentinelOne': {'detected': False, 'version': '1.0.19.234', 'result': None, 'update': '20180830'}, 'eGambit': {'detected': False, 'version': None, 'result': None, 'update': '20180920'}, 'Fortinet': {'detected': False, 'version': '5.4.247.0', 'result': None, 'update': '20180920'}, 'AVG': {'detected': False, 'version': '18.4.3895.0', 'result': None, 'update': '20180920'}, 'Cybereason': {'detected': False, 'version': '1.2.27', 'result': None, 'update': '20180225'}, 'Avast': {'detected': False, 'version': '18.4.3895.0', 'result': None, 'update': '20180920'}, 'CrowdStrike': {'detected': False, 'version': '1.0', 'result': None, 'update': '20180723'}, 'Qihoo-360': {'detected': False, 'version': '1.0.0.1120', 'result': None, 'update': '20180920'}}, 'scan_id': '7afb56dd48565c3c9804f683c80ef47e5333f847f2d3211ec11ed13ad36061e1-1537477486', 'sha1': '3b1333f826e5fe36395042fe0f1b895f4a373f1b', 'resource': '7afb56dd48565c3c9804f683c80ef47e5333f847f2d3211ec11ed13ad36061e1', 'response_code': 1, 'scan_date': '2018-09-20 21:04:46', 'permalink': 'https://www.virustotal.com/file/7afb56dd48565c3c9804f683c80ef47e5333f847f2d3211ec11ed13ad36061e1/analysis/1537477486/', 'verbose_msg': 'Scan finished, information embedded', 'total': 68, 'positives': 1, 'sha256': '7afb56dd48565c3c9804f683c80ef47e5333f847f2d3211ec11ed13ad36061e1', 'md5': '54cb91395cdaad9d47882533c21fc0e9'}
```
### Reference :
- [VirusTotal Public API v2.0](https://www.virustotal.com/en/documentation/public-api/)
