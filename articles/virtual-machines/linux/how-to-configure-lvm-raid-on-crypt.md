---
title: LvM és RAID konfigurálása titkosított eszközökön – Azure lemeztitkosítás
description: Ez a cikk utasításokat tartalmaz az LVM és a RAID linuxos virtuális gépek titkosított eszközökön történő konfigurálásához.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657443"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>LVM és RAID konfigurálása titkosított eszközökön

Ez a cikk lépésről lépésre bemutatja a logikai kötetkezelés (LVM) és a RAID titkosított eszközökön történő végrehajtásának lépéseit. A folyamat a következő környezetekre vonatkozik:

- Linux disztribúciók
    - RHEL 7,6+
    - Ubuntu 18.04+
    - SUSE 12+
- Az Azure Disk Encryption egymenetes bővítménye
- Azure Disk Encryption kétmenetes bővítmény


## <a name="scenarios"></a>Forgatókönyvek

Az ebben a cikkben szereplő eljárások a következő eseteket támogatják:  

- LVM konfigurálása titkosított eszközökre (LVM-on-crypt)
- Raid konfigurálása titkosított eszközökön (RAID-on-crypt)

Az alapul szolgáló eszköz vagy eszközök titkosítása után létrehozhatja az LVM vagy RAID struktúrákat a titkosított réteg en. 

A fizikai kötetek (TV-k) a titkosított rétegen jönnek létre. A fizikai kötetek a kötetcsoport létrehozásához használatosak. Hozza létre a köteteket, és adja hozzá a szükséges bejegyzéseket az /etc/fstab.You create the volumes and add the required entries on /etc/fstab. 

![Az LVM-szerkezetek rétegeinek diagramja](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Hasonlóképpen a RAID-eszköz a lemezeken lévő titkosított réteg tetején jön létre. A RAID-eszköz tetején egy fájlrendszer jön létre, amelyet az /etc/fstab kapcsolóhoz normál eszközként adnak hozzá.

## <a name="considerations"></a>Megfontolandó szempontok

Javasoljuk, hogy használja lvm-on-crypt. A RAID egy olyan lehetőség, ha az LVM nem használható adott alkalmazás- vagy környezetkorlátozások miatt.

A **UseformatAll titkosítási formát** fogja használni. Erről a beállításról a Linux os virtuális [gépeken lévő adatlemezekhez használja a UsetheFormatAll funkciót.](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms)

Bár ezt a módszert használhatja, amikor az operációs rendszert is titkosítja, itt csak az adatmeghajtóktitkosítását használjuk.

Az eljárások feltételezik, hogy [linuxos virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux) és rövid útmutatóban már áttekintette az előfeltételeket az Azure Disk Encryption [forgatókönyvekben: Linuxos virtuális gép létrehozása és titkosítása az Azure CLI-vel.](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart)

Az Azure Disk Encryption kétmenetű verziója egy eprecation elérési úton, és a továbbiakban nem használható az új titkosítások.

## <a name="general-steps"></a>Általános lépések

Az "on-crypt" konfigurációk használata esetén használja az alábbi eljárásokban ismertetett eljárást.

>[!NOTE] 
>Változókat használunk a cikkben. Ennek megfelelően cserélje le az értékeket.

### <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése 
A következő parancsok nem kötelezőek, de azt javasoljuk, hogy alkalmazza őket egy újonnan telepített virtuális gépen (VM).

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI:

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
### <a name="attach-disks-to-the-vm"></a>Lemezek csatolása a virtuális géphez
Ismételje meg a `$N` következő parancsokat a virtuális géphez csatolni kívánt új lemezek száma esetén.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Annak ellenőrzése, hogy a lemezek a virtuális géphez vannak-e csatlakoztatva
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Csatolt lemezek listája a PowerShellben](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Az Azure CLI csatolt lemezeinek listája](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portál:

![A portálhoz csatlakoztatott lemezek listája](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

Operációs rendszer:

```bash
lsblk 
```
![Az operációs rendszerhez csatlakoztatott lemezek listája](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>A titkosítandó lemezek konfigurálása
Ez a konfiguráció az operációs rendszer szintjén történik. A megfelelő lemezek az Azure Disk Encryption hagyományos titkosításához vannak konfigurálva:

- A fájlrendszerek a lemezek tetején jönnek létre.
- Ideiglenes csatlakoztatási pontok jönnek létre a fájlrendszerek csatlakoztatására.
- A fájlrendszerek az /etc/fstab kapcsolón vannak konfigurálva, hogy a rendszerindításkor legyenek csatlakoztatva.

Ellenőrizze az új lemezekhez rendelt eszközbetűjelet. Ebben a példában négy adatlemezt használunk.

```bash
lsblk 
```
![Az operációs rendszerhez csatlakoztatott adatlemezek](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Fájlrendszer létrehozása az egyes lemezek tetején
Ez a parancs egy ext4 fájlrendszer létrehozását teszi meg minden olyan lemezen, amelyet a "for" ciklus "in" részén határoztak meg.

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Ext4 fájlrendszer létrehozása](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Keresse meg a közelmúltban létrehozott fájlrendszerek univerzálisan egyedi azonosítóját (UUID), hozzon létre egy ideiglenes mappát, adja hozzá a megfelelő bejegyzéseket az /etc/fstab mappához, és csatlakoztassa az összes fájlrendszert.

Ez a parancs a "for" ciklus "in" részén definiált lemezeken is iterálja:

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Ellenőrizze, hogy a lemezek megfelelően vannak-e csatlakoztatva
```bash
lsblk
```
![Csatlakoztatott ideiglenes fájlrendszer jegyzéke](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Ellenőrizze azt is, hogy a lemezek konfigurálva vannak-e:

```bash
cat /etc/fstab
```
![Konfigurációs információk az fstab segítségével](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Az adatlemezek titkosítása
PowerShell kulcstitkosítási kulcs (KEK) használatával:

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

Azure CLI kek használatával:

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
![Titkosítási állapot a PowerShellben](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Titkosítási állapot az Azure CLI-ben](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portál:

![Titkosításállapota a portálon](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Operációs rendszer szintje:

```bash
lsblk
```
![Titkosítási állapot az operációs rendszerben](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

A kiterjesztés hozzáadja a fájlrendszereket a /var/lib/azure_disk_encryption_config/azure_crypt_mount (régi titkosítás) vagy az /etc/crypttab (új titkosítások) alkalmazáshoz.

>[!NOTE] 
>Ne módosítsa ezeket a fájlokat.

Ez a fájl gondoskodik a lemezek aktiválásáról a rendszerindítási folyamat során, hogy az LVM vagy a RAID később használhassa őket. 

Ne aggódjon a fájl csatlakoztatási pontjai miatt. Az Azure Disk Encryption elveszíti azt a képességét, hogy a lemezeket normál fájlrendszerként csatlakoztatva kapja, miután fizikai kötetet vagy RAID-eszközt hoztunk létre a titkosított eszközök önrajta. (Ez eltávolítja a fájlrendszer formátumát, amelyet az előkészítési folyamat során használtunk.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Az ideiglenes mappák és az ideiglenes fstab bejegyzések eltávolítása
Leválaszthatja a fájlrendszereket az LVM részeként használt lemezekről.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
És távolítsa el az /etc/fstab bejegyzéseket:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Ellenőrizze, hogy a lemezek nincsenek-e csatlakoztatva, és hogy az /etc/fstab bejegyzéseket eltávolították-e.

```bash
lsblk
```
![Az ideiglenes fájlrendszerek leválasztásának ellenőrzése](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

És ellenőrizze, hogy a lemezek konfigurálva vannak-e:
```bash
cat /etc/fstab
```
![Az ideiglenes fstab bejegyzések eltávolításának ellenőrzése](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Az LVM-on-crypt lépései
Most, hogy az alapul szolgáló lemezek titkosítva vannak, létrehozhatja az LVM-struktúrákat.

Az eszköznév használata helyett használja a /dev/mapper elérési utat az egyes lemezekhez fizikai kötet létrehozásához (a lemez tetején lévő kriptarétegen, nem pedig magán a lemezen).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>LVM konfigurálása a titkosított rétegek en
#### <a name="create-the-physical-volumes"></a>A fizikai kötetek létrehozása
Figyelmeztetést fog kapni, amely megkérdezi, hogy nem baj-e a fájlrendszer aláírásának törlése. Folytassuk **az y**, vagy használja echo **"y",** ahogy az ábrán látható:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Fizikai kötet létrehozásának ellenőrzése](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>A /dev/mapper/device neveket itt ki kell cserélni a tényleges értékekre az **lsblk**kimenete alapján.

#### <a name="verify-the-information-for-physical-volumes"></a>A fizikai kötetek adatainak ellenőrzése
```bash
pvs
```

![A fizikai kötetekre vonatkozó információk](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>A kötetcsoport létrehozása
Hozza létre a kötetcsoportot a már inicializált eszközökkel:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>A kötetcsoport adatainak ellenőrzése

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![A kötetcsoport adatai](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Logikai kötetek létrehozása

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>A létrehozott logikai kötetek ellenőrzése

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Logikai kötetek adatai](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Fájlrendszerek létrehozása a logikai kötetek struktúráinak tetején

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Az új fájlrendszerek csatlakoztatási pontjainak létrehozása

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

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Annak ellenőrzése, hogy az új fájlrendszerek csatlakoztatva vannak-e

```bash
lsblk -fs
df -h
```
![Információk a csatlakoztatott fájlrendszerekhez](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

Az **Lsblk**ezen változatán a függőségeket fordított sorrendben megjelenítő eszközöket soroljuk fel. Ez a beállítás segít azonosítani a logikai kötet szerint csoportosított eszközöket az eredeti /dev/sd[disk] eszköznevek helyett.

Fontos, hogy győződjön meg arról, hogy a **failfail** beállítás hozzá van adva a csatlakoztatási pont beállításait az Azure Disk Encryption által titkosított eszközön létrehozott LVM-kötetek csatlakoztatási pont beállításait. Megakadályozza, hogy az operációs rendszer elakadjon a rendszerindítási folyamat során (vagy karbantartási módban).

Ha nem használja a **failopciót:**

- Az operációs rendszer soha nem jut el abba a fázisba, amelyben az Azure Disk Encryption elindul, és az adatlemezek zárolása és csatlakoztatása. 
- A titkosított lemezek zárolása a rendszerindítási folyamat végén lesz feloldva. Az LVM-kötetek és fájlrendszerek automatikusan csatlakoztatva lesznek, amíg az Azure Disk Encryption fel nem oldja azokat. 

Tesztelheti a virtuális gép újraindítását, és ellenőrizheti, hogy a fájlrendszerek is automatikusan csatlakoztatva vannak-e a rendszerindítási idő után. Ez a folyamat a fájlrendszerek számától és méretétől függően több percet is igénybe vehet.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Indítsa újra a virtuális gépet, és ellenőrizze az újraindítás után

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>A RAID-on-crypt lépései
Most, hogy az alapul szolgáló lemezek titkosítva vannak, folytathatja a RAID-struktúrák létrehozását. A folyamat megegyezik az LVM-vel, de az eszköznév használata helyett használja a /dev/mapper elérési utakat az egyes lemezekhez.

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
![Információ a konfigurált RAID-hez az mdadm paranccsal](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>A /dev/mapper/device neveket itt ki kell cserélni a tényleges értékekre az **lsblk**kimenete alapján.

### <a name="checkmonitor-raid-creation"></a>RAID-létrehozás ellenőrzése/monitorozása
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Raid állapota](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Fájlrendszer létrehozása az új RAID-eszköz tetején
```bash
mkfs.ext4 /dev/md10
```

Hozzon létre egy új csatlakoztatási pontot a fájlrendszerhez, adja hozzá az új fájlrendszert az /etc/fstab fájlhoz, és csatlakoztassa:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Ellenőrizze, hogy az új fájlrendszer csatlakoztatva van-e:

```bash
lsblk -fs
df -h
```
![Információk a csatlakoztatott fájlrendszerekhez](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Fontos, hogy győződjön meg arról, hogy a **failfail** beállítás hozzá van adva a csatlakoztatási pont beállításait a RAID-kötetek létrehozott az Azure Disk Encryption által titkosított eszközön létrehozott. Megakadályozza, hogy az operációs rendszer elakadjon a rendszerindítási folyamat során (vagy karbantartási módban).

Ha nem használja a **failopciót:**

- Az operációs rendszer soha nem jut el abba a fázisba, amelyben az Azure Disk Encryption elindul, és az adatlemezek zárolása és csatlakoztatása.
- A titkosított lemezek zárolása a rendszerindítási folyamat végén lesz feloldva. A RAID-kötetek és a fájlrendszerek automatikusan csatlakoztatva lesznek, amíg az Azure Disk Encryption fel nem oldja azokat.

Tesztelheti a virtuális gép újraindítását, és ellenőrizheti, hogy a fájlrendszerek is automatikusan csatlakoztatva vannak-e a rendszerindítási idő után. Ez a folyamat a fájlrendszerek számától és méretétől függően több percet is igénybe vehet.

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

