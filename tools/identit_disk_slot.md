## 磁盘槽位确定


总结下定位硬盘槽位的方法, 一般可以直接通过  /proc/scsi/scsi 查看，但在发生热插拔磁盘的情况下信息可能不准确,
sg3_util 工具处理这种问题，在生成环境目前试验并不成功，应该跟具体的硬件支持有关系。




``
[root@xxxx ~]# rpm -qf /usr/bin/sg_map
sg3_utils-1.28-10.el6_7.x86_64
[root@xxxx ~]# rpm -qf /usr/bin/lsscsi
lsscsi-0.23-3.el6.x86_64
[root@xxxx ~]# lsscsi  -t
[0:0:0:0]    disk    sas:0x4433221103000000          /dev/sda 
[0:0:1:0]    disk    sas:0x5000c50088037451          /dev/sdb 
[0:0:2:0]    disk    sas:0x5000c5008818e3bd          /dev/sdc 
[0:0:3:0]    disk    sas:0x5000c5008822d761          /dev/sdd 
[0:0:4:0]    disk    sas:0x5000c5007f6bcde5          /dev/sde 
[1:0:0:0]    disk    sas:0x5000c5008818a509          /dev/sdf 
[1:0:1:0]    disk    sas:0x5000c500881388f5          /dev/sdg 
[1:0:2:0]    disk    sas:0x5000c50088138499          /dev/sdh 
[1:0:5:0]    disk    sas:0x5000c5008f1091b9          /dev/sdi 
[2:0:0:0]    disk    sas:0x5000c5008818b8d1          /dev/sdj 
[2:0:1:0]    disk    sas:0x5000c5008818ad09          /dev/sdk 
[2:0:2:0]    disk    sas:0x5000c5007f70adb1          /dev/sdl 
[2:0:3:0]    disk    sas:0x5000c50088197ac5          /dev/sdm 
```

```
[root@xxxx ~]# cat /proc/scsi/scsi 
Attached devices:
Host: scsi0 Channel: 00 Id: 00 Lun: 00
  Vendor: ATA      Model: INTEL SSDSC2BW48 Rev: DC32
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi0 Channel: 00 Id: 01 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi0 Channel: 00 Id: 02 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi0 Channel: 00 Id: 03 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi0 Channel: 00 Id: 04 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi1 Channel: 00 Id: 00 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi1 Channel: 00 Id: 01 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi1 Channel: 00 Id: 02 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi2 Channel: 00 Id: 00 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi2 Channel: 00 Id: 01 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi2 Channel: 00 Id: 02 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi2 Channel: 00 Id: 03 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06
Host: scsi1 Channel: 00 Id: 05 Lun: 00
  Vendor: SEAGATE  Model: ST4000NM0023     Rev: 0004
  Type:   Direct-Access                    ANSI  SCSI revision: 06

```

```
[root@xxxx ~]# sg_map -i
/dev/sg0  /dev/sda  ATA       INTEL SSDSC2BW48  DC32
/dev/sg1  /dev/sdb  SEAGATE   ST4000NM0023      0004
/dev/sg2  /dev/sdc  SEAGATE   ST4000NM0023      0004
/dev/sg3  /dev/sdd  SEAGATE   ST4000NM0023      0004
/dev/sg4  /dev/sde  SEAGATE   ST4000NM0023      0004
/dev/sg5  /dev/sdf  SEAGATE   ST4000NM0023      0004
/dev/sg6  /dev/sdg  SEAGATE   ST4000NM0023      0004
/dev/sg7  /dev/sdh  SEAGATE   ST4000NM0023      0004
/dev/sg8  /dev/sdi  SEAGATE   ST4000NM0023      0004
/dev/sg9  /dev/sdj  SEAGATE   ST4000NM0023      0004
/dev/sg10  /dev/sdk  SEAGATE   ST4000NM0023      0004
/dev/sg11  /dev/sdl  SEAGATE   ST4000NM0023      0004
/dev/sg12  /dev/sdm  SEAGATE   ST4000NM0023      0004
```
