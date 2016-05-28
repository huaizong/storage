# MegaCli 使用


## 查看RAID卡所有信息

```
[root@xxx ~]#  /opt/MegaRAID/MegaCli/MegaCli64 -cfgdsply -aALL 
                                     
==============================================================================
Adapter: 0
Product Name: LSI MegaRAID SAS 9261-8i
Memory: 512MB
BBU: Absent
Serial No: 
==============================================================================
Number of DISK GROUPS: 16

DISK GROUP: 0
Number of Spans: 1
SPAN: 0
Span Reference: 0x00
Number of PDs: 1
Number of VDs: 1
Number of dedicated Hotspares: 0
Virtual Drive Information:
Virtual Drive: 0 (Target Id: 0)
Name                :
RAID Level          : Primary-0, Secondary-0, RAID Level Qualifier-0
Size                : 3.636 TB
Sector Size         : 512
Parity Size         : 0
State               : Optimal
Strip Size          : 64 KB
Number Of Drives    : 1
Span Depth          : 1
Default Cache Policy: WriteBack, ReadAheadNone, Cached, Write Cache OK if Bad BBU
Current Cache Policy: WriteBack, ReadAheadNone, Cached, Write Cache OK if Bad BBU
Default Access Policy: Read/Write
Current Access Policy: Read/Write
Disk Cache Policy   : Enabled
Encryption Type     : None
Default Power Savings Policy: Controller Defined
Current Power Savings Policy: None
Can spin up in 1 minute: Yes
LD has drives that support T10 power conditions: No
LD's IO profile supports MAX power savings with cached writes: No
Is VD Cached: Yes
Cache Cade Type : Read Only
Physical Disk Information:
Physical Disk: 0
Enclosure Device ID: 28
Slot Number: 0
Drive's position: DiskGroup: 0, Span: 0, Arm: 0
Enclosure position: N/A
Device Id: 15
WWN: 50014ee2077b0c43
Sequence Number: 2
Media Error Count: 0
Other Error Count: 523
Predictive Failure Count: 0
Last Predictive Failure Event Seq Number: 0
PD Type: SATA

Raw Size: 3.638 TB [0x1d1c0beb0 Sectors]
Non Coerced Size: 3.637 TB [0x1d1b0beb0 Sectors]
Coerced Size: 3.637 TB [0x1d1a94800 Sectors]
Sector Size:  0
Firmware state: Online, Spun Up
Device Firmware Level: 1K01
Shield Counter: 0
Successful diagnostics completion on :  N/A
SAS Address(0): 0x5001c450006b00a3
Connected Port Number: 0(path0) 
Inquiry Data:      WD-WCC130213870WDC WD4000FYYZ-01UL1B0                  01.01K01
FDE Capable: Not Capable
FDE Enable: Disable
Secured: Unsecured
Locked: Unlocked
Needs EKM Attention: No
Foreign State: None 
Device Speed: 6.0Gb/s 
Link Speed: 6.0Gb/s 
Media Type: Hard Disk Device
Drive:  Not Certified
Drive Temperature :36C (96.80 F)
PI Eligibility:  No 
Drive is formatted for PI information:  No
PI: No PI
Drive's NCQ setting : N/A
Port-0 :
Port status: Active
Port's Linkspeed: 6.0Gb/s 
Drive has flagged a S.M.A.R.T alert : No
...
```

###  强制使用 write cache
```
[root@xxx ~]#  /opt/MegaRAID/MegaCli/MegaCli64 -LDSetProp CachedBadBBU -Lall -aALL
Set Write Cache OK if bad BBU on Adapter 0, VD 0 (target id: 0) success
...
[root@xxx ~]#  /opt/MegaRAID/MegaCli/MegaCli64 -LDSetProp WB -Lall -aALL
                                     
Adapter 0: Get BBU Status Failed.

So policy Change to WB will not come into effect immediately

Set Write Policy to WriteBack on Adapter 0, VD 0 (target id: 0) success
...
```


### 查看电池状态

```
[root@xxx ~]# /opt/MegaRAID/MegaCli/MegaCli64 -AdpBbuCmd -GetBbuStatus -aALL
                                     
BBU status for Adapter: 0

BatteryType: BBU
Voltage: 3913 mV
Current: 0 mA
Temperature: 44 C
Battery State: Optimal
BBU Firmware Status:

  Charging Status              : None
  Voltage                                 : OK
  Temperature                             : OK
  Learn Cycle Requested                   : No
  Learn Cycle Active                      : No
  Learn Cycle Status                      : OK
  Learn Cycle Timeout                     : No
  I2c Errors Detected                     : No
  Battery Pack Missing                    : No
  Battery Replacement required            : No
  Remaining Capacity Low                  : No
  Periodic Learn Required                 : No
  Transparent Learn                       : No
  No space to cache offload               : No
  Pack is about to fail & should be replaced : No
  Cache Offload premium feature required  : No
  Module microcode update required        : No

BBU GasGauge Status: 0x0028 
Relative State of Charge: 96 %
Charger Status: Complete
Remaining Capacity: 505 mAh
Full Charge Capacity: 531 mAh
isSOHGood: Yes

Exit Code: 0x00
```
