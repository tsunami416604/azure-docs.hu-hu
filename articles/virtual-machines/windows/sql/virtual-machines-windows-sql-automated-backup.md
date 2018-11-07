---
title: Automatikus biztonsági mentése az SQL Server 2014 az Azure Virtual Machines |} A Microsoft Docs
description: Ismerteti az automatikus biztonsági mentés szolgáltatás az SQL Server 2014 virtuális gépek Azure-ban futó. Ez a cikk csak a virtuális gépek a Resource Manager használatával.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: jroth
ms.openlocfilehash: 0a1ad6d50c624115bab7ad09ff0e30a36e7df500
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256612"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatikus biztonsági mentés az SQL Server 2014 virtuális gépeken (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [Az SQL Server 2016 és 2017.](virtual-machines-windows-sql-automated-backup-v2.md)

Automatikus biztonsági mentés automatikusan konfigurálja a [Managed Backup a Microsoft Azure-bA](https://msdn.microsoft.com/library/dn449496.aspx) az összes meglévő és új adatbázis egy SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális gépen. Ez lehetővé teszi, hogy az adatbázis rendszeres biztonsági mentések, amelyek ténylegesen használják a tartós az Azure blob storage-bA konfigurálása. Automatikus biztonsági mentés függ a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Automatikus biztonsági mentés használ, fontolja meg a következő előfeltételek vonatkoznak:

**Operációs rendszer**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**Az SQL Server-verzióval vagy-kiadással**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automatikus biztonsági mentés együttműködik az SQL Server 2014-re. Ha SQL Server 2016/2017 használ, automatikus biztonsági mentés v2 használatával készítsen biztonsági másolatot az adatbázisokról. További információkért lásd: [automatikus biztonsági mentés v2 az SQL Server 2016 az Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

**Adatbázis-konfiguráció**:

- Céladatbázis a teljes helyreállítási modellt kell használnia. További információ arról, hogyan befolyásolják a teljes helyreállítási modell biztonsági mentések: [biztonsági mentés alatt a teljes helyreállítási modell](https://technet.microsoft.com/library/ms190217.aspx).
- Céladatbázis alapértelmezett SQL Server-példányon kell lennie. Az SQL Server IaaS-bővítmény nem támogatja a megnevezett példány.

> [!NOTE]
> Automatikus biztonsági mentés az SQL Server IaaS-ügynök bővítmény támaszkodik. Aktuális SQL virtuálisgép-katalógus rendszerképek alapértelmezés szerint ez a bővítmény hozzáadása. További információkért lásd: [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások

A következő táblázat ismerteti a beállításokat, amelyeket az automatikus biztonsági mentés állítható be. A tényleges konfigurációs lépések eltérőek lehetnek, ha használja az Azure portal vagy az Azure Windows PowerShell-parancsokat.

| Beállítás | Címtartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Automatikus biztonsági mentés** | Engedélyezés/letiltás (letiltva) | Engedélyezi vagy letiltja az automatikus biztonsági mentés az SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális gép. |
| **Megőrzési idő** | 1 – 30 napig (30 nap) | A biztonsági másolatok megőrzése (nap) száma. |
| **Tárfiók** | Azure Storage-fiók | Blob storage-ban automatikus biztonsági mentés fájlok tárolására szolgáló Azure storage-fiókkal. Ezen a helyen tárolja az összes biztonsági mentési fájlokat tároló jön létre. Elnevezési szabálya a biztonságimásolat-fájl tartalmazza a dátum, idő és a gép neve. |
| **Titkosítás** | Engedélyezés/letiltás (letiltva) | Engedélyezi vagy letiltja a titkosítást. Ha engedélyezve van a titkosítás, a biztonsági mentés visszaállításához használt tanúsítványok találhatók a megadott tárfiók ugyanazon `automaticbackup` tároló ugyanazt az elnevezési konvenciót használ. A jelszó is módosul, ha egy új tanúsítvány hozza létre ezt a jelszót, de a régi tanúsítvány továbbra is a korábbi biztonsági másolatok visszaállításához. |
| **Jelszó** | Jelszó | A titkosítási kulcsok jelszava. Ez csak akkor szükséges, ha engedélyezve van-e a titkosítás. Annak érdekében, hogy a titkosított biztonsági másolat visszaállítása, rendelkeznie kell a helyes jelszót és a kapcsolódó az időben, a biztonsági mentéshez használt tanúsítvány. |

## <a name="configure-in-the-portal"></a>Konfigurálás a portálon

Az Azure portal segítségével a konfigurálása az automatikus biztonsági mentés, üzembe helyezés során vagy a meglévő SQL Server 2014 virtuális gépekhez.

## <a name="configure-new-vms"></a>Új virtuális gép konfigurálása

Az Azure portal segítségével automatikus biztonsági mentés konfigurálása a Resource Manager-alapú üzemi modellben létrehozott egy új SQL Server 2014 virtuális gépet.

Az a **SQL Server-beállítások** ablaktáblán válassza előbb **automatikus biztonsági mentés**. Az alábbi Azure-portál képernyőképe látható a **SQL automatikus biztonsági mentés** beállításait.

![SQL automatikus biztonsági mentés konfigurálása az Azure Portalon](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Meglévő virtuális gép konfigurálása

Meglévő SQL Server virtuális gépek esetében válassza ki az SQL Server virtuális gépet. Válassza ki a **SQL Server-konfiguráció** szakaszban a virtuális gép **beállítások**.

![SQL automatikus biztonsági mentés a meglévő virtuális gépek](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Az a **SQL Server-konfiguráció** ablaktáblán kattintson a **szerkesztése** gomb az automatikus biztonsági mentés szakaszban.

![Meglévő virtuális gépek számára az SQL automatikus biztonsági mentés konfigurálása](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Ha elkészült, kattintson a **OK** gomb alján a **SQL Server-konfiguráció** beállításait a módosítások mentéséhez.

Ha első alkalommal engedélyezi automatikus biztonsági mentés, akkor az Azure az SQL Server IaaS-ügynök úgy konfigurálja a háttérben. Ebben az időszakban az Azure Portalon nem jelenik meg, hogy az automatikus biztonsági mentés konfigurálva van-e. Várjon néhány percet az ügynök telepítve van, úgy konfigurálni. Ezt követően az Azure-portálon jelenik meg az új beállítások.

> [!NOTE]
> Automatikus biztonsági mentés egy sablon használatával is konfigurálhatja. További információkért lásd: [automatikus biztonsági mentés az Azure gyorsindítási sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>A PowerShell konfigurálása

Automatikus biztonsági mentés konfigurálása a PowerShell használatával is. Mielőtt hozzákezd, tegye a következőket:

- [Töltse le és telepítse a legújabb Azure PowerShell](https://aka.ms/webpi-azps).
- Nyissa meg a Windows Powershellt, és társítsa a fiókját a **Connect-AzureRmAccount** parancsot.

### <a name="install-the-sql-iaas-extension"></a>Az SQL IaaS-bővítményének telepítése
Ha Ön egy SQL Server virtuális gépet az Azure Portalról, az SQL Server IaaS-bővítmény már telepítve kell lennie. Megadhatja, hogy ha, telepítve van a virtuális gép meghívásával **Get-AzureRmVM** parancsot, és megvizsgálja a **bővítmények** tulajdonság.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions
```

Ha az SQL Server IaaS-ügynök bővítmény telepítve van, megjelenik az "SqlIaaSAgent" vagy "SQLIaaSExtension" jelenik. **ProvisioningState** esetében a kiterjesztést is jelenítsen meg a "sikeres üzenet".

Ha van telepítve, vagy nem telepítése sikertelen volt, a következő paranccsal telepítheti. A virtuális gép nevére és erőforráscsoportjára csoporton felül is meg kell adnia a régió (**$region**), amely a virtuális gép található.

```powershell
$region = "EASTUS2"
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Ha a bővítmény nem telepítette, a bővítmény telepítése újraindítja az SQL Server szolgáltatást.

### <a id="verifysettings"></a> Jelenlegi beállítások ellenőrzése

Ha engedélyezte az automatikus biztonsági mentés üzembe helyezés során, a PowerShell használatával a jelenlegi konfiguráció ellenőrzéséhez. Futtassa a **Get-AzureRmVMSqlServerExtension** parancsot, és vizsgálja meg a **AutoBackupSettings** tulajdonság:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Kimenetet kell kapnia a következőhöz hasonló:

```
Enable                      : False
EnableEncryption            : False
RetentionPeriod             : -1
StorageUrl                  : NOTSET
StorageAccessKey            : 
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : 
FullBackupFrequency         : 
FullBackupStartTime         : 
FullBackupWindowHours       : 
LogBackupFrequency          : 
```

A kimenet mutatja, hogy ha **engedélyezése** értékre van állítva **false (hamis)**, akkor el kell automatikus biztonsági mentés engedélyezése. A jó hír az, hogy engedélyezze, és ugyanúgy, mint az automatikus biztonsági mentés konfigurálása. További információ a következő szakaszban talál.

> [!NOTE] 
> Ha a módosítás után azonnal ellenőrizze a beállításokat, lehetséges, hogy kap vissza a régi konfigurációs értékeket. Várjon néhány percet, és ellenőrizze a beállításokat, győződjön meg arról, hogy a módosítások lettek alkalmazva a újra.

### <a name="configure-automated-backup"></a>Automatikus biztonsági mentés konfigurálása
Automatikus biztonsági mentés engedélyezése, valamint feltárhatja, hogy bármikor a konfigurációjának és viselkedésének módosítása a PowerShell használatával is.

Először válasszon ki vagy hozzon létre egy tárfiókot, a biztonsági mentési fájlokat. A következő parancsfájl egy storage-fiókot választja, vagy hoz létre, ha még nem létezik.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }
```

> [!NOTE]
> Automatikus biztonsági mentés nem támogatja a tárolását biztonsági mentések prémium szintű storage-ban, de a Premium Storage használata Virtuálisgép-lemezek biztonsági másolatait is igénybe vehet.

Ezután a **New-AzureRmVMSqlServerAutoBackupConfig** paranccsal engedélyezheti és konfigurálhatja az automatikus biztonsági mentés beállításainak biztonsági másolatok tárolására az Azure storage-fiókban. Ebben a példában a biztonsági mentések 10 napig őrződnek meg. A második parancs **Set-AzureRmVMSqlServerExtension**, frissíti a megadott Azure virtuális gép ezekkel a beállításokkal.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Telepítse és konfigurálja az SQL Server IaaS-ügynök több percet igénybe vehet.

> [!NOTE]
> Vannak más beállításokat a **New-AzureRmVMSqlServerAutoBackupConfig** , amely csak az SQL Server 2016 és az automatikus biztonsági mentés v2 vonatkoznak. Az SQL Server 2014 nem támogatja a következő beállításokat: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**,  **FullBackupStartHour**, **FullBackupWindowInHours**, és **LogBackupFrequencyInMinutes**. Ezeket a beállításokat az SQL Server 2014 virtuális gép kísérli meg, ha nem történt hiba, de nem a beállítások legyenek alkalmazva. Ha szeretné ezeket a beállításokat az SQL Server 2016 virtuális gép használja, lásd: [automatikus biztonsági mentés v2 az SQL Server 2016 az Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

Titkosítás engedélyezéséhez módosítsa a át az előző parancsfájlt a **EnableEncryption** paraméterrel együtt (biztonságos karakterlánc) jelszavát a **CertificatePassword** paraméter. A következő parancsfájl lehetővé teszi, hogy az előző példában az automatikus biztonsági mentés beállításait, és hozzáadja a titkosítás.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Ellenőrizze a beállításokat is vonatkozik, [az automatikus biztonsági mentés konfigurációjának ellenőrzése](#verifysettings).

### <a name="disable-automated-backup"></a>Automatikus biztonsági mentés letiltása

Automatikus biztonsági mentés letiltása, futtassa ugyanezt a szkriptet nélkül a **-engedélyezése** paramétert a **New-AzureRmVMSqlServerAutoBackupConfig** parancsot. Hiányában a **-engedélyezése** paraméter jelzi a parancs a funkció letiltásához. Csakúgy, mint a telepítés, automatikus biztonsági mentés letiltása több percig is eltarthat.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Példaszkript

Az alábbi parancsfájlt, amely testre szabható, engedélyezheti és konfigurálhatja az automatikus biztonsági mentés a virtuális gép változók biztosít. Az Ön esetében szükség lehet igényei alapján a parancsfájl testreszabása. Kellene például, hogy hajtsa végre a módosításokat, ha szeretne titkosítás engedélyezése vagy letiltása a rendszeradatbázisok biztonsági mentése.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = “Azure region name such as EASTUS2”
$storage_accountname = “storageaccountname”
$storage_resourcegroupname = $resourcegroupname
$retentionperiod = 10

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension

Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzureRmStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzureRmStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Figyelés

Automatikus biztonsági mentés az SQL Server 2014 monitorozásához két fő lehetősége van. Automatikus biztonsági mentés az SQL Server felügyelt biztonsági mentési funkciót használja, mert ugyanazok a figyelési módszerek alkalmazhatók.

Először is lekérdezheti az állapot a meghívásával [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Vagy nem kérdezhető le a [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) táblázat értékű függvény.

> [!NOTE]
> A felügyelt biztonsági mentés az SQL Server 2014 sémája **msdb.smart_admin**. Az SQL Server 2016-ban ez változott **msdb.managed_backup**, és a referencia-témakörei a újabb sémát használni. Az SQL Server 2014, hogy továbbra is, de a **smart_admin** minden felügyelt biztonsági mentésének objektum séma.

Egy másik lehetőség, hogy az értesítések a beépített az adatbázisbeli levelezés szolgáltatás előnyeit.

1. Hívja a [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) tárolt eljárás egy e-mail-címet hozzárendelni a **SSMBackup2WANotificationEmailIds** paraméter. 
1. Engedélyezése [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) az e-mailek küldése az Azure virtuális gépből.
1. Az SMTP-kiszolgáló és a felhasználó neve használatával konfigurálja az adatbázisbeli levelezést. Konfigurálhatja az adatbázisbeli levelezés, az SQL Server Management Studio vagy a Transact-SQL parancsokkal. További információkért lásd: [az adatbázisbeli levelezés](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurálja az adatbázisbeli levelezés használandó SQL Server Agent](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Győződjön meg arról, hogy az SMTP-portját is engedélyezve van a helyi virtuális gép tűzfal és a hálózati biztonsági csoportot a virtuális gép számára.

## <a name="next-steps"></a>További lépések

Automatikus biztonsági mentés konfigurálása Azure virtuális gépek felügyelt biztonsági mentési. Ezért fontos, hogy [a felügyelt biztonsági mentés az SQL Server 2014-dokumentációban](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Megkeresheti a további biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépeken a következő cikkben talál útmutatást: [biztonsági mentése és visszaállítása az SQL Server az Azure Virtual machines gépeken](virtual-machines-windows-sql-backup-recovery.md).

Más elérhető automation-feladatokkal kapcsolatos további információkért lásd: [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

További információ az Azure virtuális gépeken futó SQL Server: [SQL Server on Azure Virtual Machines – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).
