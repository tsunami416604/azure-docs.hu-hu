---
title: Az LVM és a RAID on-crypt beállítása Linux virtuális gépen
description: Ez a cikk utasításokat tartalmaz lvm és RAID konfigurálása a kripta Linux virtuális gépeken.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 78ba47ba887cf7c90adf70d9d444fbd8a3c721cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284908"
---
# <a name="how-to-configure-lvm-and-raid-on-crypt"></a>Az LVM és a RAID titkosítás beállítása a kriptában

Ez a dokumentum egy lépésről-lépésre folyamat arról, hogyan kell elvégezni LVM a kripta és raid a kripta konfigurációk.

### <a name="environment"></a>Környezet

- Linux disztribúciók
    - RHEL 7,6+
    - Ubuntu 18.04+
    - SUSE 12+
- ADE egymenetes
- ADE kettős áthaladás


## <a name="scenarios"></a>Forgatókönyvek

**Ez a forgatókönyv az ADE kétfázisú és egymenetes bővítményekre vonatkozik.**  

- LVM konfigurálása titkosított eszközökre (LVM-on-Crypt)
- Raid konfigurálása titkosított eszközökön (RAID-on-Crypt)

Miután az alapul szolgáló eszköz(ek) titkosítva vannak, akkor létrehozhatja az LVM vagy RAID struktúrákat a titkosított réteg tetején. A fizikai kötetek (PV) a titkosított rétegen jönnek létre.
A fizikai kötetek a kötetcsoport létrehozásához használatosak.
Hozza létre a köteteket, és adja hozzá a szükséges bejegyzéseket az /etc/fstab.You create the volumes and add the required entries on /etc/fstab. 

![Csatlakoztatott lemezek ellenőrzése](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Hasonlóképpen a RAID-eszköz a lemezeken lévő titkosított réteg tetején jön létre. A RAID-eszköz tetején fájlrendszer jön létre, és az /etc/fstab kapcsolóhoz hozzáadja normál eszközként.

### <a name="considerations"></a>Megfontolandó szempontok

Az ajánlott módszer az LVM-on-Crypt.

Raid figyelembe kell venni, ha LVM nem használható, mert az adott alkalmazás / környezet korlátai.

A Use the EncryptFormatAll opciót fogja használni, https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vmsa funkcióval kapcsolatos információk a következők: .

Bár ez a módszer az operációs rendszer titkosításakor is elvégezhető, csak az adatmeghajtók titkosítása.

Ez az eljárás feltételezi, hogy már áttekintette https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux az https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstartitt említett előfeltételeket: és itt .

Az ADE kettős áthaladási verziója eprecation útvonalon van, és a továbbiakban nem használható az új ADE-titkosításokon.

### <a name="procedure"></a>Eljárás

Az "on crypt" konfigurációk használatakor az alábbi eljárást fogja követni:

>[!NOTE] 
>Változókat használunk a dokumentumban, ennek megfelelően cseréljük le az értékeket.
## <a name="general-steps"></a>Általános lépések
### <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése 
>[!NOTE] 
>Bár ez nem kötelező, azt javasoljuk, hogy alkalmazza ezt egy újonnan üzembe helyezett virtuális gép.

PowerShell
```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Cli:
```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Lemezek csatlakoztatása a virtuális géphez:
Ismétlés $N Virtuálisgép PowerShellhez csatlakoztatni kívánt új lemezek száma esetén
```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```
Cli:
```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```
### <a name="verify-the-disks-are-attached-to-the-vm"></a>Ellenőrizze, hogy a lemezek a virtuális géphez vannak-e csatlakoztatva:
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Ellenőrizze a csatlakoztatott](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png) PowerShell CLI lemezeket:
```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Ellenőrizze a csatlakoztatott](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png) lemezek ![CLI Portál:](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png) Ellenőrizze a csatlakoztatott lemezek CLI operációs rendszer:
```bash
lsblk 
```
![Csatlakoztatott lemezek ellenőrzése](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)
### <a name="configure-the-disks-to-be-encrypted"></a>A titkosítandó lemezek konfigurálása
Ez a konfiguráció történik, hogy az operációs rendszer szintjén, a megfelelő lemezek vannak konfigurálva a hagyományos ADE titkosítás:

A fájlrendszerek a lemezek tetején jönnek létre.

Ideiglenes csatlakoztatási pontok jönnek létre a fájlrendszerek csatlakoztatására.

A fájlrendszerek az /etc/fstab kapcsolón vannak konfigurálva a rendszerindításkor való csatlakoztatáshoz.

Ellenőrizze az eszköz betűrendelt az új lemezek, ebben a példában vagyunk négy adatlemezek

```bash
lsblk 
```
![Csatlakoztatott lemezek ellenőrzése](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-filesystem-on-top-of-each-disk"></a>Hozzon létre egy fájlrendszert az egyes lemezek tetején.
Ez a parancs a "for" ciklus "in" részén definiált lemezeken egy ext4 fájlrendszer létrehozását is megindítja.
```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Ellenőrizze a csatlakoztatott](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png) lemezek operációs rendszer Keresse meg a UUID a fájlrendszerek nemrég létrehozott, hozzon létre egy ideiglenes mappát csatlakoztatni, add hozzá a megfelelő bejegyzéseket /etc/fstab és csatolja az összes fájlrendszer.

Ez a parancs a "for" ciklus "in" részén definiált lemezeken is iterálja:
```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 
### <a name="verify-the-disks-are-mounted-properly"></a>Ellenőrizze, hogy a lemezek megfelelően vannak-e csatlakoztatva:
```bash
lsblk
```
![Ellenőrizze temp filesystems](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png) szerelt és konfigurálva:
```bash
cat /etc/fstab
```
![Fstab ellenőrzése](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)
### <a name="encrypt-the-data-disks"></a>Az adatlemezek titkosítása:
A KEK használatával a PowerShell:
```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```
CLI a KEK használatával:
```bash
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>A titkosítási állapot ellenőrzése

Csak akkor folytassa a következő lépéssel, ha az összes lemez titkosítva van.

PowerShell:
```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Ellenőrizze titkosítás](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png) ps CLI:
```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Ellenőrizze titkosításCLI](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png) ![portál:](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png) Ellenőrizze a titkosítási operációs rendszer operációs rendszer szintje:
```bash
lsblk
```
![Titkosítási CLI ellenőrzése](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

A kiterjesztés hozzáadja a fájlrendszereket a "/var/lib/azure_disk_encryption_config/azure_crypt_mount" (egy régi titkosításhoz), vagy hozzáadódik az "/etc/crypttab" (új titkosítások) értékhez.

Ne módosítsa ezeket a fájlokat.

Ez a fájl lesz ügyelve aktiválása ezeket a lemezeket a rendszerindítási folyamat során, így később használható LVM vagy RAID. 

Ne aggódj a mount pontokat ebben a fájlban, mint ADE elveszíti a képességét, hogy a lemezek szerelt, mint egy normál fájlrendszer után hozunk létre egy fizikai kötet vagy raid eszköz tetején a titkosított eszközök (amely megszabadulni a fájlrendszer formátumban használtunk során előkészítési folyamat).
### <a name="remove-the-temp-folders-and-temp-fstab-entries"></a>Az ideiglenes mappák és a temp fstab bejegyzések eltávolítása
Az LVM részeként használt lemezeken leválasztjuk a fájlrendszereket
```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
És távolítsa el az /etc/fstab bejegyzéseket:
```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Ellenőrizze, hogy a lemezek nincsenek-e csatlakoztatva, és hogy az /etc/fstab bejegyzéseket eltávolították-e.
```bash
lsblk
```
![Ellenőrizze temp filesystems](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png) lenemcsatlakoztatott és konfigurálva:
```bash
cat /etc/fstab
```
![Ellenőrizze temp fstab bejegyzések eltávolítása](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)
## <a name="for-lvm-on-crypt"></a>LVM-on-crypt esetén
Most, hogy az alapul szolgáló lemezek titkosítva vannak, folytathatja az LVM-struktúrák létrehozását.

Az eszköznév használata helyett használja a /dev/mapper elérési utat az egyes lemezekhez fizikai kötet létrehozásához (a lemez tetején lévő kriptarétegen, amely nem a lemezen található).
### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>LVM konfigurálása a titkosított rétegek en
#### <a name="create-the-physical-volumes"></a>A fizikai kötetek létrehozása
Figyelmeztetést fog kapni, amely megkérdezi, hogy nem baj-e a fájlrendszer aláírásának törlése. 

Folytathatja az "y" beírását, vagy használhatja az "y" visszhangot az alábbi módon:
```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![pvcreate](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)
>[!NOTE] 
>A /dev/mapper/device nevekitt kell cserélni a tényleges értékek et az lsblk kimenete alapján.
#### <a name="verify-the-physical-volumes-information"></a>A fizikai kötetek adatainak ellenőrzése
```bash
pvs
```
![fizikai kötetek ellenőrzése 1](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)
#### <a name="create-the-volume-group"></a>A kötetcsoport létrehozása
A VG létrehozása a már inicializált eszközökkel
```bash
vgcreate vgdata /dev/mapper/
```
### <a name="check-the-volume-group-information"></a>A kötetcsoport adatainak ellenőrzése
```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![fizikai térfogatok ellenőrzése 2](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)
#### <a name="create-logical-volumes"></a>Logikai kötetek létrehozása
```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 
#### <a name="check-the-logical-volumes-created"></a>A létrehozott logikai kötetek ellenőrzése
```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![ellenőrizze lvs](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)
#### <a name="create-filesystems-on-top-of-the-logical-volumes-structures"></a>Fájlrendszerek létrehozása a logikai kötet(ek) struktúráján felül
```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```
#### <a name="create-the-mount-points-for-the-new-filesystems"></a>Az új fájlrendszerek csatlakoztatási pontjainak létrehozása
```bash
mkdir /data0
mkdir /data1
```
#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Adja hozzá az új fájlrendszereket az /etc/fstab fájlhoz, és szerelje fel őket
```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
#### <a name="verify-that-the-new-filesystems-are-mounted"></a>Annak ellenőrzése, hogy az új fájlrendszerek csatlakoztatva vannak-e
```bash
lsblk -fs
df -h
```
![logikai](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png) kötetek ellenőrzése Az lsblk ezen változatában felsoroljuk azokat az eszközöket, amelyek a fordított sorrendtől való függőségeket mutatják, ez a beállítás segít azonosítani a logikai kötet szerint csoportosított eszközöket az eredeti /dev/sd[disk] eszköznevek helyett.

Fontos: Győződjön meg arról, hogy a "nofail" beállítás hozzá van adva az ADE titkosított eszközön létrehozott LVM-kötetek csatlakoztatási pontbeállításaihoz. Fontos, hogy elkerüljék az operációs rendszer elakad a boot folyamat (vagy karbantartási módban). 

A titkosított lemez feloldása a rendszerindítási folyamat végén történik, az LVM kötetek és a fájlrendszerek automatikusan csatlakoztatva lesznek.

Ha a failfail opciót nem használja, az operációs rendszer soha nem jut el abba a fázisba, ahol az ADE elindul, és az adatlemez(ek) zárolása és csatlakoztatása.

Tesztelheti a virtuális gép újraindítását, és a fájlrendszerek érvényesítése is automatikusan csatlakoztatva van a rendszerindítási idő után. 

Vegye figyelembe, hogy ez a folyamat a fájlrendszerek számától és a fájlrendszerek méretétől függően több percet is igénybe vehet
#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Indítsa újra a virtuális gépet, és ellenőrizze az újraindítás után
```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="for-raid-on-crypt"></a>For RAID-on-Crypt
Most az alapul szolgáló lemezek titkosítva vannak, továbbra is létrehozhatja a RAID-struktúrákat, ugyanúgy, mint az LVM, az eszköz név használata helyett, használja a /dev/mapper elérési utat az egyes lemezekhez.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Raid konfigurálása a lemezek titkosított rétege indikáta tetején
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![mdadm létrehozása](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)
>[!NOTE] 
>A /dev/mapper/device nevekitt kell cserélni a tényleges értékek et az lsblk kimenete alapján.
#### <a name="checkmonitor-the-raid-creation"></a>A RAID létrehozásának ellenőrzése/figyelése:
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![ellenőrizze mdadm](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)
#### <a name="create-a-filesystem-on-top-of-the-new-raid-device"></a>Hozzon létre egy fájlrendszert az új Raid eszköz tetején:
```bash
mkfs.ext4 /dev/md10
```
Hozzon létre egy új csatlakoztatási pontot a fájlrendszerhez, adja hozzá az új fájlrendszert az /etc/fstab fájlhoz, és csatlakoztassa
```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```
Annak ellenőrzése, hogy az új fájlrendszerek csatlakoztatva vannak-e
```bash
lsblk -fs
df -h
```
![ellenőrizze mdadm](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Fontos: Győződjön meg arról, hogy a "nofail" beállítás hozzá van adva az ADE titkosított eszközön létrehozott RAID-kötetek csatlakoztatási pontbeállításaihoz. 

Nagyon fontos, hogy elkerüljék az operációs rendszer elakad a boot folyamat során (vagy karbantartási módban). 

A titkosított lemez a rendszerindítási folyamat végén lesz feloldva, és a RAID-kötetek és a fájlrendszerek automatikusan csatlakoztatva lesznek, amíg az ADE fel nem oldja őket, ha a nofail opciót nem használja.

Az operációs rendszer soha nem jut el abba a fázisba, ahol az ADE elindul, és az adatlemezek zárolása és csatlakoztatása történik.

Tesztelheti a virtuális gép újraindítását, és a fájlrendszerek érvényesítése is automatikusan csatlakoztatva van a rendszerindítási idő után. Vegye figyelembe, hogy ez a folyamat a fájlrendszerek számától és a fájlrendszerek méretétől függően több percet is igénybe vehet
```bash
shutdown -r now
```
És mikor tud bejelentkezni:
```bash
lsblk
df -h
```
## <a name="next-steps"></a>További lépések

- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)

