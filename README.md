# WD-EX2-Ultra_Raid
Some notes to create a RAID with USB Disks on a WD MyCloud EX2 Ultra.

## SSH
Activate SSH.\
Connect with `ssh sshd@{ip}`.

## Check orginal HDDs (we want to know the SDx)
`cat /var/www/xml/current_hd_info.xml`

> ```
> <?xml version="1.0" encoding="UTF-8"?>
> <config>
>   <hd_info>
>     <device_info>
>       <device_num>2</device_num>
>       <item>
>         <device_name>sda</device_name>
>         <scsi>0</scsi>
>         <vendor>WD</vendor>
>         <model>WDC WD80EFAX-68KNBN0</model>
>         <allowed>1</allowed>
>         <temp_limit>0</temp_limit>
>         <hd_serial>VGG2XMAG</hd_serial>
>         <hd_size>7814026584</hd_size>
>         <hd_GiB_size>7452.0</hd_GiB_size>
>         <sdx2_size>7809830912</sdx2_size>
>         <part_cnt>4</part_cnt>
>       </item>
>       <item>
>         <device_name>sdf</device_name>
>         <scsi>1</scsi>
>         <vendor>WD</vendor>
>         <model>WDC WD80EFAX-68KNBN0</model>
>         <allowed>1</allowed>
>         <temp_limit>0</temp_limit>
>         <hd_serial>VDKVN5HM</hd_serial>
>         <hd_size>7814026584</hd_size>
>         <hd_GiB_size>7452.0</hd_GiB_size>
>         <sdx2_size>7809830912</sdx2_size>
>         <part_cnt>4</part_cnt>
>       </item>
>     </device_info>
>   </hd_info>
> </config>
> ```

## Get USB Drive `/dev/SDx`
`cat /tmp/usb_port_map`

> Note:
> It can happen, that your preinstalled disk move to another `/dev/SDx`.
> For example, on my EX2 the original HDDs where `SDA` and `SDB`.
> After connecting the USB HDD Box, the where `SDA` and `SDF`.
> ```
> root@MyCloudEX2Ultra dev # cat /tmp/usb_port_map
> sdb : 0
> sdc : 1
> sdd : 2
> sde : 3
> ```

## Check current md's
`ls /dev/md*`

> ```
> brw-rw----    1 root     root        9,   0 Dec  9 14:05 /dev/md0
> brw-rw----    1 root     root      259,   2 Dec  9 14:05 /dev/md0p1
> brw-rw----    1 root     root      259,   3 Dec  9 14:05 /dev/md0p2
> brw-rw----    1 root     root        9,   1 Dec  9 14:05 /dev/md1
> ```

## Create RAID
`mdadm --create /dev/md2 --level=5 --raid-devices=4 /dev/sdb /dev/sdc /dev/sdd /dev/sde`

## Check RAID Status
`cat /proc/mdstat`

`mdadm /dev/md2 --detail`

> Compare with `mdadm /dev/md1 --detail`
> There is a UUID. Sure we can use that later.
