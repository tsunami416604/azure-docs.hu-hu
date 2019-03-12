---
title: Az SQL Server 2016-ra 2017/Azure virtuális gépek biztonsági mentési v2 automatikus |} A Microsoft Docs
description: Ismerteti az automatikus biztonsági mentés szolgáltatás az SQL Server 2016 2017/virtuális gépek Azure-ban. Ez a cikk csak a virtuális gépek a Resource Manager használatával.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 540acd1735eb539ecaac468e74511ba5f751278f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780014"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Biztonsági mentési v2 automatikus az Azure virtuális gépeken (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatikusan beállítja az automatikus biztonsági mentés v2 [Managed Backup a Microsoft Azure-bA](https://msdn.microsoft.com/library/dn449496.aspx) az összes meglévő és új adatbázis-beli virtuális gépen futó SQL Server 2016/2017 Standard, Enterprise és Developer kiadásainak. Ez lehetővé teszi, hogy az adatbázis rendszeres biztonsági mentések, amelyek ténylegesen használják a tartós az Azure blob storage-bA konfigurálása. Automatikus biztonsági mentés v2 függ a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Automatikus biztonsági mentés v2 használja, tekintse át a következő előfeltételek vonatkoznak:

**Operációs rendszer**:

- Windows Server 2012 R2
- Windows Server 2016

**Az SQL Server-verzióval vagy-kiadással**:

- SQL Server 2016: Fejlesztői, Standard vagy Enterprise
- SQL Server 2017: Fejlesztői, Standard vagy Enterprise

> [!IMPORTANT]
> Automatikus biztonsági mentés v2 működik az SQL Server 2016 vagy újabb verziója. Ha SQL Server 2014 használ, automatikus biztonsági mentés v1 használatával készítsen biztonsági másolatot az adatbázisokról. További információkért lásd: [automatikus biztonsági mentés az SQL Server 2014 az Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

**Adatbázis-konfiguráció**:

- Céladatbázis a teljes helyreállítási modellt kell használnia. További információ arról, hogyan befolyásolják a teljes helyreállítási modell biztonsági mentések: [biztonsági mentés alatt a teljes helyreállítási modell](https://technet.microsoft.com/library/ms190217.aspx).
- Rendszeradatbázisokban nem rendelkezik teljes helyreállítási modell használatára. Naplóalapú biztonsági mentések esetében a modell vagy MSDB van szükség, ha a teljes helyreállítási modellt kell használnia.
- Céladatbázis kell lennie, vagy az alapértelmezett SQL Server-példányon, vagy egy [megfelelően telepített](virtual-machines-windows-sql-server-iaas-faq.md#administration) megnevezett példány. 

> [!NOTE]
> Automatikus biztonsági mentés támaszkodik a **SQL Server IaaS-ügynök bővítmény**. Aktuális SQL virtuálisgép-katalógus rendszerképek alapértelmezés szerint ez a bővítmény hozzáadása. További információkért lásd: [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások
A következő táblázat ismerteti az automatikus biztonsági mentés v2-ben konfigurált beállítások. A tényleges konfigurációs lépések eltérőek lehetnek, ha használja az Azure portal vagy az Azure Windows PowerShell-parancsokat.

### <a name="basic-settings"></a>Alapbeállítások

| Beállítás | Címtartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Automatikus biztonsági mentés** | Engedélyezés/letiltás (letiltva) | Engedélyezi vagy letiltja az automatikus biztonsági mentés az SQL Server 2016/2017 Developer, Standard vagy Enterprise rendszert futtató Azure virtuális gép. |
| **Megőrzési idő** | 1 – 30 napig (30 nap) | A biztonsági mentések megőrzési idejét napok száma. |
| **Tárfiók** | Azure Storage-fiók | Blob storage-ban automatikus biztonsági mentés fájlok tárolására szolgáló Azure storage-fiókkal. Ezen a helyen tárolja az összes biztonsági mentési fájlokat tároló jön létre. Elnevezési szabálya a biztonságimásolat-fájl tartalmazza a dátum, idő és adatbázis GUID-ja. |
| **Titkosítás** |Engedélyezés/letiltás (letiltva) | Engedélyezi vagy letiltja a titkosítást. Ha engedélyezve van a titkosítás, a biztonsági mentés visszaállításához használt tanúsítványok találhatók a megadott tárfiókhoz. Használja ugyanazt **automaticbackup** tárolóhoz, az ugyanazt az elnevezési konvenciót. A jelszó is módosul, ha egy új tanúsítvány hozza létre ezt a jelszót, de a régi tanúsítvány továbbra is a korábbi biztonsági másolatok visszaállításához. |
| **Jelszó** |Jelszó | A titkosítási kulcsok jelszava. Ez a jelszó csak akkor szükséges, ha engedélyezve van-e a titkosítás. Annak érdekében, hogy a titkosított biztonsági másolat visszaállítása, rendelkeznie kell a helyes jelszót és a kapcsolódó az időben, a biztonsági mentéshez használt tanúsítvány. |

### <a name="advanced-settings"></a>Speciális beállítások

| Beállítás | Címtartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Rendszer-adatbázisok biztonsági mentése** | Engedélyezés/letiltás (letiltva) | Ha engedélyezve van, ez a funkció is adatbázisok biztonsági mentését a rendszer: Master, MSDB, és modell. Az msdb adatbázisban és a modell adatbázisok esetén ellenőrizze, hogy azok teljes körű helyreállítási módban, ha azt szeretné, hogy a napló biztonsági másolat. Naplóalapú biztonsági mentések főkiszolgáló soha nem kerül. És a TempDB nem készült biztonsági másolat készít. |
| **Biztonsági mentés ütemezése** | Manuális vagy automatikus (automatikus) | Alapértelmezés szerint a biztonsági mentési ütemezés automatikusan határozza meg a napló növekedése alapján. Manuális biztonsági mentés ütemezése a felhasználó adja meg a biztonsági mentések időtartományából. Ebben az esetben a biztonsági másolatok csak kerülhet sor, a megadott gyakorisággal és az adott napon az adott időszakban. |
| **Teljes biztonsági mentés gyakorisága** | Napi/heti | Teljes biztonsági mentések gyakoriságát. Mindkét esetben teljes biztonsági mentés megkezdése a következő ütemezett időszakban. Heti kiválasztásakor a biztonsági mentések sikerült esetleg több napon, amíg az összes adatbázis sikeresen készített biztonsági másolatot. |
| **Teljes biztonsági mentés kezdő időpontja** | 00:00 – 23:00 (01:00) | Indítsa el egy adott nap során, ami teljes biztonsági mentés akkor kerül sor. |
| **Teljes biztonsági mentés időkerete** | 1 – 23 óra (1 óra) | Egy adott napon, mely során a teljes biztonsági mentések sor kerülhet az időkeretnél időtartama. |
| **Naplóalapú biztonsági mentés gyakorisága** | 5 – 60 perc (60 perc) | Naplóalapú biztonsági mentések gyakoriságát. |

## <a name="understanding-full-backup-frequency"></a>Teljes biztonsági mentés gyakorisága ismertetése
Fontos tudni, hogy a napi és heti teljes biztonsági mentések közötti különbség. Vegye figyelembe a következő két példa forgatókönyveket.

### <a name="scenario-1-weekly-backups"></a>1. forgatókönyv: Heti biztonsági mentései
Van egy SQL Server virtuális Gépet, amely tartalmaz egy nagy méretű adatbázisok száma.

Hétfő a következő beállításokkal engedélyezi az automatikus biztonsági mentés v2:

- Biztonsági mentés ütemezése: **Manuális**
- Teljes biztonsági mentés gyakorisága: **Hetente**
- Teljes biztonsági mentés kezdő időpontja: **01:00**
- Teljes biztonsági mentés időkerete: **1 óra**

Ez azt jelenti, hogy a következő elérhető biztonsági mentés időszakának kedd 1 órakor 1 órán keresztül. Ekkor az automatikus biztonsági mentés megkezdése adatbázisainak biztonsági mentése a egy egyszerre. Ebben a forgatókönyvben az adatbázisok elég nagyok ahhoz, hogy a teljes biztonsági mentés az első néhány adatbázisok befejezéséhez. Azonban egy óra múlva nem minden, az adatbázisok biztonsági mentése volt.

Ha ez történik, az automatikus biztonsági mentés kezdődik, adatbázisainak biztonsági mentése a fennmaradó a következő napon, szerda, 1 Órakor egy órán keresztül. Ha nem minden adatbázis biztonsági mentése volt az adott, próbálkozik újra a következő nap egyszerre. Ez továbbra is fennáll, addig, amíg az összes adatbázis lett sikeresen készített biztonsági másolatot.

Miután ismét eléri kedd, automatikus biztonsági mentés megkezdi az adatbázisok biztonsági mentése az összes újra.

Ebből a forgatókönyvből megtudhatja, hogy az automatikus biztonsági mentés csak akkor működik, a megadott időtartományon belül, és minden egyes adatbázis biztonsági mentését hetente egyszer. Ez is azt mutatja, hogy a biztonsági mentések esetleg több napon abban az esetben, ha már nem minden biztonsági mentés végrehajtásához egy egyetlen nap alatt.

### <a name="scenario-2-daily-backups"></a>2. forgatókönyv: Napi biztonsági mentések
Van egy SQL Server virtuális Gépet, amely tartalmaz egy nagy méretű adatbázisok száma.

Hétfő a következő beállításokkal engedélyezi az automatikus biztonsági mentés v2:

- Biztonsági mentés ütemezése: Kézi
- Teljes biztonsági mentés gyakorisága: Napi
- Teljes biztonsági mentés kezdő időpontja: 22:00
- Teljes biztonsági mentés időkerete: 6 óra

Ez azt jelenti, hogy a következő elérhető biztonsági mentés időszakának hétfő alapszintűről 6 órán át. Ekkor az automatikus biztonsági mentés megkezdése adatbázisainak biztonsági mentése a egy egyszerre.

Ezt követően kedden legalább 10-6 órán át, az összes adatbázis teljes biztonsági mentések indítsa el újra.

> [!IMPORTANT]
> Napi biztonsági mentések ütemezésekor ajánlott úgy ütemezni egy széles időtartomány győződjön meg, hogy az összes adatbázis biztonsági mentésre alkalmas ezen az időn belül. Ez különösen fontos abban az esetben, ahol van egy nagy mennyiségű adat biztonsági mentését.

## <a name="configure-in-the-portal"></a>Konfigurálás a portálon

Az Azure portal segítségével a konfigurálása az automatikus biztonsági mentés v2, üzembe helyezés során vagy a meglévő SQL Server 2016 2017/virtuális gépek.

## <a name="configure-for-new-vms"></a>Az új virtuális gépek konfigurálása

Az Azure portal segítségével a konfigurálása az automatikus biztonsági mentés v2, amikor egy új SQL Server 2016 vagy 2017 virtuális gépet hoz létre a Resource Manager-alapú üzemi modellben.

Az a **SQL Server-beállítások** ablaktáblán válassza előbb **automatikus biztonsági mentés**. Az alábbi Azure-portál képernyőképe látható a **SQL automatikus biztonsági mentés** beállításait.

![SQL automatikus biztonsági mentés konfigurálása az Azure Portalon](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Automatikus biztonsági mentés v2 alapértelmezés szerint le van tiltva.

## <a name="configure-existing-vms"></a>Meglévő virtuális gép konfigurálása

Meglévő SQL Server virtuális gépek esetében válassza ki az SQL Server virtuális gépet. Válassza ki a **SQL Server-konfiguráció** szakaszban a virtuális gép **beállítások**.

![SQL automatikus biztonsági mentés a meglévő virtuális gépek](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)

Az a **SQL Server-konfiguráció** beállításait, majd kattintson a **szerkesztése** gomb az automatikus biztonsági mentés szakaszban.

![Meglévő virtuális gépek számára az SQL automatikus biztonsági mentés konfigurálása](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration-edit.png)

Ha elkészült, kattintson a **OK** gomb alján a **SQL Server-konfiguráció** beállításait a módosítások mentéséhez.

Ha első alkalommal engedélyezi automatikus biztonsági mentés, akkor az Azure az SQL Server IaaS-ügynök úgy konfigurálja a háttérben. Ebben az időszakban az Azure Portalon nem jelenik meg, hogy az automatikus biztonsági mentés konfigurálva van-e. Várjon néhány percet az ügynök telepítve van, úgy konfigurálni. Ezt követően az Azure-portálon jelenik meg az új beállítások.

## <a name="configure-with-powershell"></a>A PowerShell konfigurálása

Automatikus biztonsági mentés v2 konfigurálása a PowerShell használatával is. Mielőtt hozzákezd, tegye a következőket:

- [Töltse le és telepítse a legújabb Azure PowerShell](https://aka.ms/webpi-azps).
- Nyissa meg a Windows Powershellt, és társítsa a fiókját a **Connect-AzAccount** parancsot.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Az SQL IaaS-bővítményének telepítése
Ha Ön egy SQL Server virtuális gépet az Azure Portalról, az SQL Server IaaS-bővítmény már telepítve kell lennie. Megadhatja, hogy ha, telepítve van a virtuális gép meghívásával **Get-azvm parancsmag** parancsot, és megvizsgálja a **bővítmények** tulajdonság.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Ha az SQL Server IaaS-ügynök bővítmény telepítve van, megjelenik az "SqlIaaSAgent" vagy "SQLIaaSExtension" jelenik. **ProvisioningState** esetében a kiterjesztést is jelenítsen meg a "sikeres üzenet". 

Ha van telepítve, vagy nem telepítése sikertelen volt, a következő paranccsal telepítheti. A virtuális gép nevére és erőforráscsoportjára csoporton felül is meg kell adnia a régió (**$region**), amely a virtuális gép található.

```powershell
$region = “EASTUS2”
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a id="verifysettings"></a> Jelenlegi beállítások ellenőrzése
Ha engedélyezte az automatikus biztonsági mentés üzembe helyezés során, a PowerShell használatával a jelenlegi konfiguráció ellenőrzéséhez. Futtassa a **Get-AzVMSqlServerExtension** parancsot, és vizsgálja meg a **AutoBackupSettings** tulajdonság:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Kimenetet kell kapnia a következőhöz hasonló:

```
Enable                      : True
EnableEncryption            : False
RetentionPeriod             : 30
StorageUrl                  : https://test.blob.core.windows.net/
StorageAccessKey            :  
Password                    : 
BackupSystemDbs             : False
BackupScheduleType          : Manual
FullBackupFrequency         : WEEKLY
FullBackupStartTime         : 2
FullBackupWindowHours       : 2
LogBackupFrequency          : 60
```

A kimenet mutatja, hogy ha **engedélyezése** értékre van állítva **false (hamis)**, akkor el kell automatikus biztonsági mentés engedélyezése. A jó hír az, hogy engedélyezze, és ugyanúgy, mint az automatikus biztonsági mentés konfigurálása. További információ a következő szakaszban talál.

> [!NOTE] 
> Ha a módosítás után azonnal ellenőrizze a beállításokat, lehetséges, hogy kap vissza a régi konfigurációs értékeket. Várjon néhány percet, és ellenőrizze a beállításokat, győződjön meg arról, hogy a módosítások lettek alkalmazva a újra.

### <a name="configure-automated-backup-v2"></a>Automatikus biztonsági mentés v2 konfigurálása
Automatikus biztonsági mentés engedélyezése, valamint feltárhatja, hogy bármikor a konfigurációjának és viselkedésének módosítása a PowerShell használatával is. 

Először válasszon ki vagy hozzon létre egy tárfiókot, a biztonsági mentési fájlokat. A következő parancsfájl egy storage-fiókot választja, vagy hoz létre, ha még nem létezik.

```powershell
$storage_accountname = “yourstorageaccount”
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Automatikus biztonsági mentés nem támogatja a tárolását biztonsági mentések prémium szintű storage-ban, de a Premium Storage használata Virtuálisgép-lemezek biztonsági másolatait is igénybe vehet.

Ezután a **New-AzVMSqlServerAutoBackupConfig** paranccsal engedélyezheti és konfigurálhatja az automatikus biztonsági mentés v2-beállítások biztonsági másolatok tárolására az Azure storage-fiókban. Ebben a példában a biztonsági mentések vannak állítva 10 napig. Rendszer-adatbázisok biztonsági mentése engedélyezve vannak. Egy időtartomány kezdve két óra 20:00-kor a heti teljes biztonsági mentések ütemezett. Naplóalapú biztonsági mentések ütemezett 30 percenként. A második parancs **Set-AzVMSqlServerExtension**, frissíti a megadott Azure virtuális gép ezekkel a beállításokkal.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Telepítse és konfigurálja az SQL Server IaaS-ügynök több percet igénybe vehet. 

Titkosítás engedélyezéséhez módosítsa a át az előző parancsfájlt a **EnableEncryption** paraméterrel együtt (biztonságos karakterlánc) jelszavát a **CertificatePassword** paraméter. A következő parancsfájl lehetővé teszi, hogy az előző példában az automatikus biztonsági mentés beállításait, és hozzáadja a titkosítás.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Ellenőrizze a beállításokat is vonatkozik, [az automatikus biztonsági mentés konfigurációjának ellenőrzése](#verifysettings).

### <a name="disable-automated-backup"></a>Automatikus biztonsági mentés letiltása
Automatikus biztonsági mentés letiltása, futtassa ugyanezt a szkriptet nélkül a **-engedélyezése** paramétert a **New-AzVMSqlServerAutoBackupConfig** parancsot. Hiányában a **-engedélyezése** paraméter jelzi a parancs a funkció letiltásához. Csakúgy, mint a telepítés, automatikus biztonsági mentés letiltása több percig is eltarthat.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
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
$backupscheduletype = "Manual"
$fullbackupfrequency = "Weekly"
$fullbackupstarthour = "20"
$fullbackupwindow = "2"
$logbackupfrequency = "30"

# ResourceGroupName is the resource group which is hosting the VM where you are deploying the SQL IaaS Extension 

Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region

# Creates/use a storage account to store the backups

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region }

# Configure Automated Backup settings

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays $retentionperiod -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Figyelés

Automatikus biztonsági mentés az SQL Server 2016/2017 monitorozásához két fő lehetősége van. Automatikus biztonsági mentés az SQL Server felügyelt biztonsági mentési funkciót használja, mert ugyanazok a figyelési módszerek alkalmazhatók.

Először is lekérdezheti az állapot a meghívásával [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Vagy nem kérdezhető le a [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) táblázat értékű függvény.

Egy másik lehetőség, hogy az értesítések a beépített az adatbázisbeli levelezés szolgáltatás előnyeit.

1. Hívja a [msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) tárolt eljárás egy e-mail-címet hozzárendelni a **SSMBackup2WANotificationEmailIds** paraméter. 
1. Engedélyezése [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) az e-mailek küldése az Azure virtuális gépből.
1. Az SMTP-kiszolgáló és a felhasználó neve használatával konfigurálja az adatbázisbeli levelezést. Konfigurálhatja az adatbázisbeli levelezés, az SQL Server Management Studio vagy a Transact-SQL parancsokkal. További információkért lásd: [az adatbázisbeli levelezés](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurálja az adatbázisbeli levelezés használandó SQL Server Agent](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Győződjön meg arról, hogy az SMTP-portját is engedélyezve van a helyi virtuális gép tűzfal és a hálózati biztonsági csoportot a virtuális gép számára.

## <a name="next-steps"></a>További lépések
Automatikus biztonsági mentés v2 Azure virtuális gépek felügyelt biztonsági mentésének konfigurálása. Ezért fontos, hogy [felügyelt biztonsági mentés a dokumentációban](https://msdn.microsoft.com/library/dn449496.aspx) viselkedését és következményeiről.

Megkeresheti a további biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépeken a következő cikkben talál útmutatást: [Biztonsági mentés és visszaállítás Azure-beli virtuális gépeken az SQL Serverhez](virtual-machines-windows-sql-backup-recovery.md).

Más elérhető automation-feladatokkal kapcsolatos további információkért lásd: [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

További információ az Azure virtuális gépeken futó SQL Server: [SQL Server on Azure Virtual Machines – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

