---
title: A Linux titkosítási állapotának ellenőrzése
description: Ez a cikk útmutatást nyújt a titkosítási állapotnak a platform és az operációs rendszer szintjén történő ellenőrzéséhez.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123418"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>A Linux titkosítási állapotának ellenőrzése 

**Ez a forgatókönyv az ADE kettős Pass és az Single-pass kiterjesztések esetében érvényes.**  
A dokumentum hatóköre egy virtuális gép titkosítási állapotának ellenőrzése különböző módszerekkel.

### <a name="environment"></a>Környezet

- Linux-disztribúciók

### <a name="procedure"></a>Eljárás

A virtuális gépeket kettős pass vagy Single-pass használatával titkosították.

A titkosítási állapot a titkosítás során vagy után is ellenőrizhető különböző módszerekkel.

>[!NOTE] 
>A dokumentumban változókat használunk, ennek megfelelően cserélje le az értékeket.

### <a name="verification"></a>Ellenőrzés

Az ellenőrzés a portál, a PowerShell, az CLI és a VM operációs rendszer oldaláról végezhető el. 

Ez az ellenőrzés az adott virtuális géphez csatlakoztatott lemezek ellenőrzésével végezhető el. 

Vagy az egyes lemezeken lévő titkosítási beállítások lekérdezésével, hogy a lemez csatlakoztatva van-e vagy le van-e kapcsolva.

A különböző érvényesítési módszerek alatt:

## <a name="using-the-portal"></a>A portál használata

Ellenőrizze a titkosítási állapotot a Azure Portal Extensions (bővítmények) szakaszának ellenőrzésével.

A **bővítmények** szakaszban láthatja a felsorolt ade-bővítményt. 

Kattintson rá, és tekintse meg az **állapotjelző üzenetet**, amely a jelenlegi titkosítási állapotot jelzi:

![1. portál-ellenőrzési szám](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

A bővítmények listájában megjelenik a megfelelő ADE-bővítmény verziója. A 0. x verzió megfelel az ade Dual-Pass és az 1. x verziónak, amely az ADE Single-pass értéknek felel meg.

További részletekért kattintson a bővítményre, majd a *részletes állapot megtekintése*lehetőségre.

A titkosítási folyamat részletesebb állapotát JSON formátumban fogja látni:

![2. portál-ellenőrzési szám](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Portál-ellenőrzési szám 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

A titkosítási állapot érvényesítésének egy másik módja a **lemezek** szakaszának megkeresése.

![Portál-ellenőrzési szám 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Ez az állapot azt jelenti, hogy a lemezeken a titkosítási beállítások vannak lepecsételve, de nem, hogy az operációs rendszer szintjén lettek titkosítva. A lemezeket a rendszer úgy látja el, hogy először lepecsételi és titkosítja őket. Ha a titkosítási folyamat meghiúsul, előfordulhat, hogy a lemezek lebélyegzettek, de nem titkosítottak. Annak ellenőrzéséhez, hogy a lemezek valóban titkosítva vannak-e, megtekintheti az egyes lemezek titkosítását az operációs rendszer szintjén.

## <a name="using-powershell"></a>A PowerShell használata

A titkosított virtuális gépek **általános** titkosítási állapotát a következő PowerShell-parancsok használatával ellenőrizheti:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![1. PowerShell-vizsgálat](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Az egyes lemezek titkosítási beállításait a következő PowerShell-parancsok használatával rögzítheti:

### <a name="single-pass"></a>Egyszeres bérlet
Ha a Single-pass, a titkosítási beállítások az egyes lemezek (operációs rendszer és adategységek) stampek, az operációs rendszer lemezének titkosítási beállításait az alábbi módon rögzítheti az egyes lemezeken:

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
![Az operációs rendszer Single pass 01 ellenőrzése](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Ha a lemez nem rendelkezik lebélyegzett titkosítási beállításokkal, a kimenet üres lesz, ahogy az alábbi ábrán látható:

![Operációs rendszer titkosítási beállításai 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Adatlemez (ek) titkosítási beállításainak rögzítése:

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
![Az adategyetlen PS 001 ellenőrzése](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Kettős menet
A kettős menetben a titkosítási beállítások a virtuálisgép-modellben vannak lepecsételve, és nem az egyes lemezeken.

Annak ellenőrzéséhez, hogy a titkosítási beállítások a kettős menetben vannak-e lepecsételve, a következő parancsokat használhatja:

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
![A kettős pass PowerShell 1 ellenőrzése](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Nem csatlakoztatott lemezek

A virtuális géphez nem csatolt lemezek titkosítási beállításainak ellenőrzéséhez.

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
## <a name="using-az-cli"></a>Az AZ parancssori felület használata

A titkosított virtuális gépek **általános** titkosítási állapotát a következő az CLI-parancsok használatával ellenőrizheti:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Általános ellenőrzés a parancssori felület használatával ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Egyszeri továbbítás
Az egyes lemezek titkosítási beállításait a következő az CLI-parancsok használatával ellenőrizheti:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Adattitkosítási beállítások](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Ha a lemez nem rendelkezik lepecsételt titkosítási beállításokkal, akkor a "lemez nincs titkosítva" érték jelenik meg.

Részletes állapot-és titkosítási beállítások:

OPERÁCIÓSRENDSZER-lemez:

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

![Egyetlen parancssori felület ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Kettős menet

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![A CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) -vel kapcsolatos általános kettős ellenőrzéssel ELLENŐRIZHETI az operációsrendszer-lemez virtuálisgép-modell tárolási profiljában található titkosítási beállításokat is:

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

![A VM-profil kettős ellenőrzése a CLI használatával ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Nem csatlakoztatott lemezek

A virtuális géphez nem csatolt lemezek titkosítási beállításainak ellenőrzéséhez.

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

A nem felügyelt lemezek az Azure Storage-fiókokban blobként tárolt VHD-fájlok.

Egy adott lemez adatainak beszerzéséhez a következőket kell megadnia:

A lemezt tartalmazó Storage-fiók azonosítója.
Az adott Storage-fiókhoz tartozó kapcsolatok karakterlánca.
A lemezt tároló tároló neve.
A lemez neve.

Ez a parancs felsorolja az összes Storage-fiók azonosítóját:

```bash
az storage account list --query [].[id] -o tsv
```
A Storage-fiók azonosítói a következő formában vannak felsorolva:

/Subscriptions/\<előfizetés-azonosító>\</Resourcegroups/erőforráscsoport neve>/Providers/Microsoft.Storage/storageaccounts/\<Storage-fiók neve>

Válassza ki a megfelelő azonosítót, és tárolja egy változóban:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
A kapcsolatok karakterlánca.

Ez a parancs beolvassa egy adott Storage-fiókhoz tartozó kapcsolatok karakterláncát, és egy változóban tárolja azt:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

A tároló neve.

A következő parancs a Storage-fiókban lévő összes tárolót listázza:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
A lemezekhez használt tároló általában VHD-k neve

Tároló nevének tárolása egy változón 
```bash
ContainerName="name of the container"
```

A lemez neve.

Ezzel a paranccsal listázhatja egy adott tároló összes blobját.
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Válassza ki a lekérdezni kívánt lemezt, és tárolja a nevét egy változóban.
```bash
DiskName="diskname.vhd"
```
A lemez titkosítási beállításainak lekérdezése
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Az operációs rendszerből
Annak ellenőrzése, hogy az adatlemez-partíciók titkosítva vannak-e (és az operációsrendszer-lemez nem)

Ha egy partíció/lemez titkosítva van, akkor a rendszer a **titkosítási** típusként jeleníti meg, ha nincs titkosítva, a **kijelző/lemez** típusaként jelenik meg.

``` bash
lsblk
```

![Operációsrendszer-Crypt réteg ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

További részleteket a következő "lsblk" változat használatával kaphat. 

Ekkor megjelenik egy, a bővítmény által csatlakoztatott **Crypt** típusú réteg.

Az alábbi példában a logikai kötetek és a normál lemezek "**kriptográfiai\_LUKS FSTYPE**" láthatók.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Operációs rendszer Crypt 2. rétege](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

További lépésként azt is ellenőrizheti, hogy az adatlemez rendelkezik-e kulcsok betöltésével

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

És mely DM-eszközök szerepelnek Crypt-ként

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption – hibaelhárítás](disk-encryption-troubleshooting.md)
