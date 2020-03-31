---
title: Linux titkosítási állapotának ellenőrzése
description: Ez a cikk a platform és az operációs rendszer szintjén a titkosítási állapot ellenőrzésére vonatkozó utasításokat tartalmaz.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123418"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Linux titkosítási állapotának ellenőrzése 

**Ez a forgatókönyv az ADE kétfázisú és egymenetes bővítményekre vonatkozik.**  
Ez a dokumentumhatókör a virtuális gépek titkosítási állapotának ellenőrzése különböző módszerekkel.

### <a name="environment"></a>Környezet

- Linux disztribúciók

### <a name="procedure"></a>Eljárás

A virtuális gép két- vagy egyszeres fázisú titkosítása megtörtént.

A titkosítási állapot a titkosítás alatt vagy után különböző módszerekkel érvényesíthető.

>[!NOTE] 
>Változókat használunk a dokumentumban, ennek megfelelően cseréljük le az értékeket.

### <a name="verification"></a>Ellenőrzés

Az ellenőrzés elvégezhető a portálról, a PowerShellből, az AZ CLI-ből és a virtuális gép operációs rendszerének oldaláról. 

Ez az ellenőrzés egy adott virtuális géphez csatlakoztatott lemezek ellenőrzésével végezhető el. 

Vagy lekérdezi a titkosítási beállításokat minden egyes lemezen, függetlenül attól, hogy a lemez csatlakoztatva van-e vagy nincs-e csatlakoztatva.

A különböző validálási módszerek alatt:

## <a name="using-the-portal"></a>A portál használata

Ellenőrizze a titkosítási állapotot az Azure Portalbővítmények szakaszának ellenőrzésével.

A **Bővítmények** szakaszban megjelenik az ADE-bővítmény. 

Kattintson rá, és vessen egy pillantást az **állapotüzenetre,** amely jelzi az aktuális titkosítási állapotot:

![1- es portálellenőrzés](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

A bővítmények listájában megjelenik a megfelelő ADE bővítményverzió. A 0.x verzió az ADE Dual-Pass-nak, az 1.x verzió pedig az ADE Single-pass-nak felel meg.

További részleteket a bővítményre kattintva, majd a *Részletes állapot megtekintése oldalon*kaphat.

A titkosítási folyamat részletesebb állapota json formátumban jelenik meg:

![2- es számú portálellenőrzés](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![3-as számú portálellenőrzés](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

A titkosítási állapot érvényesítésének másik módja a **Lemezek** szakasz.

![4-es számú portálellenőrzés](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Ez az állapot azt jelenti, hogy a lemezek titkosítási beállításai le vannak pecsételve, de nem azt, hogy az operációs rendszer szintjén ténylegesen titkosítva voltak.This status means the disks have encryption settings stamped but not that they were actually encrypted at OS level. A lemezeket szándékosan először lebélyegezi, majd később titkosítja. Ha a titkosítási folyamat sikertelen, előfordulhat, hogy a lemezek lepecsételve végződnek, de nem titkosítva. Annak ellenőrzéséhez, hogy a lemezek valóban titkosítva vannak-e, az operációs rendszer szintjén ellenőrizheti az egyes lemezek titkosítását.

## <a name="using-powershell"></a>A PowerShell használata

A titkosított virtuális gépek **általános** titkosítási állapotát a következő PowerShell-parancsokkal ellenőrizheti:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![PowerShell 1 ellenőrzése](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

A titkosítási beállításokat az egyes lemezekről a következő PowerShell-parancsokkal rögzítheti:

### <a name="single-pass"></a>Egyszeres áthaladás
Ha egymenetes, a titkosítási beállítások bélyegzővel minden egyes lemez (operációs rendszer és az adatok), akkor rögzítse az operációs rendszer lemeztitkosítási beállításait egy menetben az alábbiak szerint:

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Az operációs rendszer egymenetes átlépésének ellenőrzése 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Ha a lemezen nincs lepecsételve titkosítási beállítás, a kimenet üres lesz az alábbiak szerint:

![Operációs rendszer titkosítási beállításai 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Adatlemez(ek) rögzítési beállításai:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Adatok ellenőrzése egyetlen ps 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Kettős áthaladás
A Dual Pass, a titkosítási beállítások a virtuális gép modell, és nem az egyes lemezeken.

A titkosítási beállítások kettős átfutási szögben való lebélyegzéséhez a következő parancsokat használhatja:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Kétmenetes PowerShell 1 ellenőrzése](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Nem csatolt lemezek

Ellenőrizze a titkosítási beállításokat a virtuális géphez nem csatolt lemezek.

### <a name="managed-disks"></a>Felügyelt lemezek
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="using-az-cli"></a>Az AZ CLI használata

A titkosított virtuális gépek **általános** titkosítási állapotát a következő AZ CLI-parancsokkal ellenőrizheti:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Általános ellenőrzés a CLI használatával ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Egymenetes bérlet
A titkosítási beállításokat az egyes lemezekről a következő AZ CLI-parancsok segítségével ellenőrizheti:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Adattitkosítási beállítások](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Abban az esetben, ha a lemez nem rendelkezik titkosítási beállításokkal, akkor a "Lemez nincs titkosítva" formában jelenik meg.

Részletes állapot- és titkosítási beállítások:

Operációs rendszer lemeze:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Adatlemezek:

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Adat egyetlen CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Kettős bérlet

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Ellenőrizze az általános ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) kettős cli segítségével Is ellenőrizze a titkosítási beállításokat a VM Model Storage profil az operációs rendszer lemez:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![A vm-profil kettős ellenőrzése a CLI használatával ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Nem csatolt lemezek

Ellenőrizze a titkosítási beállításokat a virtuális géphez nem csatolt lemezek.

### <a name="managed-disks"></a>Felügyelt lemezek

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Nem felügyelt lemezek

A nem felügyelt lemezek olyan Virtuális merevlemez-fájlok, amelyek az Azure storage-fiókokban lapblobként tárolódnak.

Egy adott lemez részleteinek megismeréséhez meg kell adnia a következőket:

A lemezt tartalmazó tárfiók azonosítója.
Az adott tárfiók csatlakozási karakterlánca.
A lemezt tároló tároló neve.
A lemez neve.

Ez a parancs felsorolja az összes tárfiók összes azonosítóját:

```bash
az storage account list --query [].[id] -o tsv
```
A tárfiók-azonosítók a következő formában jelennek meg:

\</subscription/subscription id>/resourceGroups/\<erőforráscsoport neve>/providers/Microsoft.StorageAccounts/\<storage fiók neve>

Válassza ki a megfelelő azonosítót, és tárolja egy változón:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
A kapcsolati karakterlánc.

Ez a parancs lekéri egy adott tárfiók kapcsolati karakterláncát, és egy változón tárolja:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

A tároló neve.

A következő parancs felsorolja a tárfiók összes tárolóját:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
A lemezekhez használt tároló neve általában "vhds"

A tároló nevének tárolása változón 
```bash
ContainerName="name of the container"
```

A lemez neve.

Ezzel a paranccsal listázhatoaz összes blobot egy adott tárolóban
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Válassza ki a lekérdezni kívánt lemezt, és tárolja a nevét egy változón.
```bash
DiskName="diskname.vhd"
```
A lemeztitkosítási beállítások lekérdezése
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Az operációs rendszerből
Ellenőrizze, hogy az adatlemez-partíciók titkosítva vannak-e (és az operációsrendszer-lemez nem)

Amikor egy partíció/lemez titkosítva van, **akkor kriptatípusként** jelenik meg, ha nem titkosított, akkor **része/lemez** típusaként jelenik meg.

``` bash
lsblk
```

![Os Crypt réteg ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

További részleteket a következő "lsblk" változatban kaphat. 

Megjelenik egy **kriptatípus-réteg,** amelyet a bővítmény csatlakoztat.

A következő példa a logikai köteteket és a normál lemezeket mutatja be, amelyek "**crypto LUKS FSTYPE " (" crypto LUKS FSTYPE " (" crypto LUKS FSTYPE " (Kripto\_LUKS FSTYPE**" típusú .

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Os Kripta réteg 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

További lépésként azt is ellenőrizheti, hogy az adatlemezen vannak-e betöltve kulcsok

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

És mely dm eszközök vannak felsorolva kriptaként

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)
