---
title: Automatizált Backup v2 SQL Server 2016/2017 Azure-beli virtuális gépekhez | Microsoft Docs
description: Ismerteti az Azure-ban futó SQL Server 2016/2017 virtuális gépek automatizált biztonsági mentési funkcióját. Ez a cikk a Resource Managert használó virtuális gépekre jellemző.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 452dfcc04d9fc9048493222ad2a82a5bcc8b78f4
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162862"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatizált Backup v2 Azure Virtual Machineshoz (Resource Manager)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Az automatizált Backup v2 automatikusan konfigurálja a [felügyelt biztonsági mentést, hogy Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) minden meglévő és új adatbázishoz egy Azure-beli virtuális gépen, amely SQL Server 2016/2017 standard, Enterprise vagy Developer kiadást futtat. Ez lehetővé teszi, hogy az Azure Blob Storage-t használó normál adatbázis-biztonsági mentéseket konfigurálja. Az automatizált Backup v2 a [SQL Server IaaS-ügynök bővítménytől](virtual-machines-windows-sql-server-agent-extension.md)függ.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Az automatikus biztonsági mentés v2-es verziójának használatához tekintse át a következő előfeltételeket:

**Operációs rendszer**:

- Windows Server 2012 R2
- Windows Server 2016

**SQL Server verzió/kiadás**:

- SQL Server 2016: fejlesztő, standard vagy Enterprise
- SQL Server 2017: fejlesztő, standard vagy Enterprise

> [!IMPORTANT]
> Az automatizált Backup v2 SQL Server 2016-es vagy újabb verziójával működik. Ha a SQL Server 2014-es verzióját használja, az adatbázisok biztonsági mentéséhez használhatja az automatizált Backup v1-et is. További információ: [SQL Server 2014 Azure Virtual Machines automatikus biztonsági mentése](virtual-machines-windows-sql-automated-backup.md).

**Adatbázis-konfiguráció**:

- A célként megadott adatbázisoknak a teljes helyreállítási modellt kell használniuk. A teljes helyreállítási modell biztonsági mentésekre gyakorolt hatásával kapcsolatos további információkért lásd: [biztonsági mentés a teljes helyreállítási modell alatt](https://technet.microsoft.com/library/ms190217.aspx).
- A rendszeradatbázisoknak nem kell teljes helyreállítási modellt használniuk. Ha azonban a modell-vagy MSDB a naplók biztonsági mentésére van szükség, teljes helyreállítási modellt kell használnia.
- A célként megadott adatbázisoknak az alapértelmezett SQL Server példányon vagy egy [megfelelően telepített](virtual-machines-windows-sql-server-iaas-faq.md#administration) elnevezett példányon kell lenniük. 

> [!NOTE]
> Az automatikus biztonsági mentés a **SQL Server IaaS-ügynök bővítményére**támaszkodik. Az aktuális SQL-virtuálisgép-katalógus lemezképei alapértelmezés szerint a bővítményt adja hozzá. További információ: [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások
A következő táblázat az automatikus Backup v2-hez konfigurálható beállításokat ismerteti. A tényleges konfigurációs lépések attól függően változnak, hogy az Azure Portal vagy az Azure Windows PowerShell-parancsokat használja-e.

### <a name="basic-settings"></a>Alapszintű beállítások

| Beállítás | Tartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Automatikus biztonsági mentés** | Engedélyezés/letiltás (letiltva) | Engedélyezheti vagy letilthatja a SQL Server 2016/2017 Developer, standard vagy Enterprise rendszerű Azure-beli virtuális gépek automatizált biztonsági mentését. |
| **Megőrzési időtartam** | 1-30 nap (30 nap) | A biztonsági másolatok megőrzéséhez szükséges napok száma. |
| **Tárfiók** | Azure Storage-fiók | Egy Azure Storage-fiók, amelyet a blob Storage-ban lévő automatizált biztonságimásolat-fájlok tárolására használ. Ezen a helyen létrejön egy tároló az összes biztonságimásolat-fájl tárolásához. A biztonságimásolat-fájl elnevezési konvenciója tartalmazza a dátum-és időpontokat, valamint az adatbázis GUID azonosítóját. |
| **Titkosítás** |Engedélyezés/letiltás (letiltva) | Engedélyezheti vagy letilthatja a titkosítást. Ha engedélyezve van a titkosítás, a biztonsági mentés visszaállításához használt tanúsítványok a megadott Storage-fiókban találhatók. Ugyanezt az **automatikus biztonsági mentési** tárolót használja ugyanazzal az elnevezési konvencióval. Ha a jelszó megváltozik, új tanúsítvány jön létre ezzel a jelszóval, de a régi tanúsítvány továbbra is a korábbi biztonsági mentéseket állítja vissza. |
| **Jelszó** |Jelszó szövege | A titkosítási kulcsok jelszava. Ez a jelszó csak akkor szükséges, ha engedélyezve van a titkosítás. A titkosított biztonsági mentés visszaállításához a biztonsági másolat készítésének időpontjában használt jelszóval és kapcsolódó tanúsítvánnyal kell rendelkeznie. |

### <a name="advanced-settings"></a>Speciális beállítások

| Beállítás | Tartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Rendszeradatbázis biztonsági mentései** | Engedélyezés/letiltás (letiltva) | Ha engedélyezve van, ez a szolgáltatás biztonsági másolatot készít a rendszeradatbázisokról is: Master, MSDB és Model. A MSDB és a Model-adatbázisok esetében ellenőrizze, hogy a rendszer teljes helyreállítási módban van-e, ha a naplók biztonsági mentését szeretné elvégezni. A rendszer nem készít biztonsági másolatokat a Master szolgáltatáshoz. És nem készül biztonsági másolat a TempDB. |
| **Biztonsági mentés ütemterve** | Manuális/automatizált (automatizált) | Alapértelmezés szerint a rendszer automatikusan meghatározza a biztonsági mentési ütemtervet a naplózási növekedés alapján. A manuális biztonsági mentési ütemezés lehetővé teszi a felhasználó számára a biztonsági mentések időablakának megadását. Ebben az esetben a biztonsági mentések csak a megadott gyakorisággal és az adott nap megadott időablakában helyezhetők el. |
| **Teljes biztonsági mentés gyakorisága** | Napi/heti | A teljes biztonsági mentés gyakorisága. Mindkét esetben a teljes biztonsági mentés a következő ütemezett időszakban kezdődik. Ha a hetente van kiválasztva, a biztonsági mentések több napig is elterjedhetnek, amíg az összes adatbázis biztonsági mentése sikeresen megtörtént. |
| **Teljes biztonsági mentés kezdési ideje** | 00:00 – 23:00 (01:00) | Egy adott nap kezdési időpontja, amely alatt a teljes biztonsági mentés elvégezhető. |
| **Teljes biztonsági mentés időablaka** | 1 – 23 óra (1 óra) | Egy adott nap időtartományának időtartama, amely során a rendszer teljes biztonsági mentést végez. |
| **Napló biztonsági mentési gyakorisága** | 5 – 60 perc (60 perc) | A naplók biztonsági másolatainak gyakorisága. |

## <a name="understanding-full-backup-frequency"></a>A teljes biztonsági mentés gyakoriságának ismertetése
Fontos megérteni a napi és heti teljes biztonsági mentések közötti különbséget. Vegye figyelembe a következő két példa forgatókönyvet.

### <a name="scenario-1-weekly-backups"></a>1\. eset: heti biztonsági másolatok
Számos nagyméretű adatbázist tartalmazó SQL Server VM rendelkezik.

Hétfőn a következő beállításokkal engedélyezheti az automatikus Backup v2-t:

- Biztonsági mentés időpontja: **manuális**
- Teljes biztonsági mentés gyakorisága: **hetente**
- Teljes biztonsági mentés kezdő időpontja: **01:00**
- Teljes biztonsági mentés időablaka: **1 óra**

Ez azt jelenti, hogy a következő elérhető biztonsági mentési ablak kedd, 1 óra. Ebben az esetben az automatikus biztonsági mentés egyszerre elindítja az adatbázisok biztonsági mentését. Ebben az esetben az adatbázisok elég nagyok ahhoz, hogy az első pár adatbázis teljes biztonsági mentése befejeződjön. Egy óra elteltével azonban nem történt meg az összes adatbázis biztonsági mentése.

Ha ez történik, az automatikus biztonsági mentés a következő napon, szerdán pedig 1 óráig a fennmaradó adatbázisok biztonsági mentését indítja el. Ha nem minden adatbázisról készült biztonsági másolat, akkor a következő napon újra próbálkozik. Ez addig folytatódik, amíg az összes adatbázist nem sikerült biztonsági mentést készíteni.

Miután a kedd ismét eléri az automatikus biztonsági mentést, megkezdi az összes adatbázis biztonsági mentését.

Ez a forgatókönyv azt mutatja be, hogy az automatikus biztonsági mentés csak a megadott időszakon belül működik, és minden adatbázis hetente egyszer biztonsági mentést készít. Ez azt is jelenti, hogy a biztonsági másolatok több napig is elterjedhetnek, ha az összes biztonsági mentést egyetlen nap alatt nem lehet elvégezni.

### <a name="scenario-2-daily-backups"></a>2\. forgatókönyv: napi biztonsági másolatok
Számos nagyméretű adatbázist tartalmazó SQL Server VM rendelkezik.

Hétfőn a következő beállításokkal engedélyezheti az automatikus Backup v2-t:

- Biztonsági mentés időpontja: manuális
- Teljes biztonsági mentés gyakorisága: naponta
- Teljes biztonsági mentés kezdő időpontja: 22:00
- Teljes biztonsági mentés időablaka: 6 óra

Ez azt jelenti, hogy a következő rendelkezésre álló biztonsági mentési ablak 6 órán át hétfő 10 ÓRAKOR. Ebben az esetben az automatikus biztonsági mentés egyszerre elindítja az adatbázisok biztonsági mentését.

Ezt követően pedig 6 órán keresztül 10 órakor, az összes adatbázis teljes biztonsági mentése újra elindul.

> [!IMPORTANT]
> A napi biztonsági mentések ütemezésekor ajánlott a széles körű időszak ütemezése, hogy az összes adatbázisról biztonsági másolat készüljön. Ez különösen fontos abban az esetben, ha nagy mennyiségű adattal kell biztonsági másolatot készíteni.

## <a name="configure-in-the-portal"></a>Konfigurálás a portálon

A Azure Portal használatával konfigurálhatja az automatizált Backup v2-t a kiépítés során, vagy a meglévő SQL Server 2016/2017 virtuális gépekhez.

## <a name="configure-for-new-vms"></a>Konfigurálás új virtuális gépekhez

Ha a Resource Manager-alapú üzemi modellben új SQL Server 2016 vagy 2017 virtuális gépet hoz létre, a Azure Portal segítségével konfigurálja az automatikus biztonsági mentés v2-es verzióját.

A **SQL Server beállítások** lapon válassza az **Engedélyezés** lehetőséget az **automatikus biztonsági mentés**területen. Az alábbi Azure Portal képernyőképen az **SQL-alapú automatikus biztonsági mentési** beállítások láthatók.

![SQL-alapú automatikus biztonsági mentési konfiguráció a Azure Portal](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Az automatizált Backup v2 alapértelmezés szerint le van tiltva.

## <a name="configure-existing-vms"></a>Meglévő virtuális gépek konfigurálása

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Meglévő SQL Server virtuális gépek esetében navigáljon az [SQL Virtual Machines erőforráshoz](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) , majd válassza a **biztonsági** másolatok lehetőséget az automatikus biztonsági mentések konfigurálásához.

![SQL automatikus biztonsági mentés meglévő virtuális gépekhez](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)


Ha elkészült, kattintson a **biztonsági mentések** beállításai oldal alján található **alkalmaz** gombra a módosítások mentéséhez.

Ha első alkalommal engedélyezi az automatikus biztonsági mentést, az Azure a háttérben konfigurálja a SQL Server IaaS-ügynököt. Ebben az időszakban előfordulhat, hogy a Azure Portal nem jelenik meg, hogy az automatikus biztonsági mentés konfigurálva van. Várjon néhány percet, amíg az ügynök telepítve van, konfigurálva van. Ezt követően a Azure Portal az új beállításokat fogja tükrözni.

## <a name="configure-with-powershell"></a>Konfigurálás a PowerShell-lel

A PowerShell segítségével konfigurálhatja az automatikus biztonsági mentés v2-es verzióját. A Kezdés előtt a következőket kell tennie:

- [Töltse le és telepítse a legújabb Azure PowerShell](https://aka.ms/webpi-azps).
- Nyissa meg a Windows PowerShellt, és társítsa azt a fiókhoz a **AzAccount** paranccsal.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Az SQL IaaS bővítmény telepítése
Ha kiépített egy SQL Server virtuális gépet a Azure Portalból, a SQL Server IaaS bővítménynek már telepítve kell lennie. A **Get-AzVM** parancs meghívásával és a **bővítmények** tulajdonság vizsgálatával megállapíthatja, hogy a virtuális géphez van-e telepítve.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Ha a SQL Server IaaS-ügynök bővítmény telepítve van, akkor az "SqlIaaSAgent" vagy "SQLIaaSExtension" néven jelenik meg. A bővítmény **ProvisioningState** a "sikeres" kifejezést is meg kell jeleníteni. 

Ha nincs telepítve vagy nem sikerült kiépíteni, akkor a következő paranccsal telepítheti. A virtuális gép neve és az erőforráscsoport mellett azt a régiót ( **$region**) is meg kell adnia, amelyben a virtuális gép található.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a id="verifysettings"></a>Aktuális beállítások ellenőrzése
Ha engedélyezte az automatikus biztonsági mentést a kiépítés során, a PowerShell segítségével ellenőrizze az aktuális konfigurációt. Futtassa a **Get-AzVMSqlServerExtension** parancsot, és vizsgálja meg a **AutoBackupSettings** tulajdonságot:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

A következőhöz hasonló kimenetnek kell megjelennie:

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

Ha a kimenet azt mutatja, hogy az **Engedélyezés** értéke **false (hamis**), akkor engedélyeznie kell az automatikus biztonsági mentést. A jó hír az, hogy az automatikus biztonsági mentést ugyanúgy engedélyezheti és konfigurálhatja. Tekintse meg ezt az információt a következő szakaszban.

> [!NOTE] 
> Ha azonnal bejelöli a beállításokat a módosítás után, akkor előfordulhat, hogy vissza fogja kérni a régi konfigurációs értékeket. Várjon néhány percet, és ellenőrizze újra a beállításokat, hogy megbizonyosodjon róla, hogy a módosítások érvénybe lépnek.

### <a name="configure-automated-backup-v2"></a>Automatikus biztonsági mentés v2 konfigurálása
A PowerShell használatával engedélyezheti az automatikus biztonsági mentést, valamint bármikor módosíthatja a konfigurációját és működését. 

Először válasszon ki vagy hozzon létre egy Storage-fiókot a biztonságimásolat-fájlokhoz. A következő parancsfájl kiválasztja a Storage-fiókot, vagy létrehozza, ha nem létezik.

```powershell
$storage_accountname = "yourstorageaccount"
$storage_resourcegroupname = $resourcegroupname

$storage = Get-AzStorageAccount -ResourceGroupName $resourcegroupname `
    -Name $storage_accountname -ErrorAction SilentlyContinue
If (-Not $storage)
    { $storage = New-AzStorageAccount -ResourceGroupName $storage_resourcegroupname `
    -Name $storage_accountname -SkuName Standard_GRS -Location $region } 
```

> [!NOTE]
> Az automatikus biztonsági mentés nem támogatja a biztonsági másolatok tárolását a Premium Storage-ban, de a Premium Storaget használó VM-lemezekről is készíthet biztonsági másolatokat.

Ezután a **New-AzVMSqlServerAutoBackupConfig** paranccsal engedélyezze és konfigurálja az automatikus Backup v2 beállításait a biztonsági másolatok Azure Storage-fiókban való tárolásához. Ebben a példában a biztonsági mentések a 10 napos megőrzésre vannak beállítva. A rendszeradatbázis biztonsági mentései engedélyezve vannak. A teljes biztonsági mentés heti rendszerességgel van ütemezve, és egy időablak két órán keresztül, 20:00-kor kezdődik. A naplók biztonsági mentései 30 percenként vannak ütemezve. A második parancs, a **set-AzVMSqlServerExtension**, frissíti a megadott Azure-beli virtuális gépet ezekkel a beállításokkal.

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

A SQL Server IaaS-ügynök telepítése és konfigurálása több percet is igénybe vehet. 

A titkosítás engedélyezéséhez módosítsa az előző szkriptet, hogy átadja a **EnableEncryption** paramétert a **CertificatePassword** paraméterhez tartozó jelszóval (Secure string) együtt. A következő parancsfájl lehetővé teszi az előző példában szereplő automatizált biztonsági mentési beállításokat, és titkosítja a titkosítást.

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

A beállítások alkalmazásának megerősítéséhez [ellenőrizze az automatikus biztonsági mentés konfigurációját](#verifysettings).

### <a name="disable-automated-backup"></a>Automatikus biztonsági mentés letiltása
Az automatikus biztonsági mentés letiltásához futtassa ugyanazt a parancsfájlt anélkül, hogy az **-enable** paramétert a **New-AzVMSqlServerAutoBackupConfig** parancsra. A **-enable** paraméter hiánya jelzi, hogy a parancs letiltja a funkciót. A telepítéshez hasonlóan több percet is igénybe vehet, hogy letiltsa az automatizált biztonsági mentést.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Példaszkript
A következő parancsfájl egy olyan változót biztosít, amely testreszabható a virtuális gép automatizált biztonsági mentésének engedélyezéséhez és konfigurálásához. Előfordulhat, hogy a saját igényeinek megfelelően testre kell szabnia a parancsfájlt. Előfordulhat például, hogy módosításokat kell végeznie, ha le szeretné tiltani a rendszeradatbázisok biztonsági mentését, vagy engedélyeznie kell a titkosítást.

```powershell
$vmname = "yourvmname"
$resourcegroupname = "vmresourcegroupname"
$region = "Azure region name such as EASTUS2"
$storage_accountname = "storageaccountname"
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

## <a name="monitoring"></a>Monitoring

A SQL Server 2016/2017-es automatikus biztonsági mentés figyeléséhez két fő lehetőség közül választhat. Mivel az automatikus biztonsági mentés a SQL Server felügyelt biztonsági mentési szolgáltatást használja, ugyanaz a figyelési módszer is érvényes mindkét eszközre.

Először is lekérdezheti az állapotot a [msdb. managed_backup. sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql)hívásával. Vagy a [msdb. managed_backup. fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tábla értékű függvény lekérdezése.

Egy másik lehetőség, hogy kihasználja az értesítések beépített Database Mail funkciójának előnyeit.

1. A [msdb. managed_backup. sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) tárolt eljárás meghívásával rendeljen hozzá egy e-mail-címet a **SSMBackup2WANotificationEmailIds** paraméterhez. 
1. Engedélyezze a [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) az e-mailek küldését az Azure-beli virtuális gépről.
1. A Database Mail konfigurálásához használja az SMTP-kiszolgálót és a felhasználónevet. SQL Server Management Studio vagy Transact-SQL-parancsokkal is konfigurálhatja a Database Mail. További információ: [Database mail](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurálja SQL Server Agent Database mail használatára](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Győződjön meg arról, hogy az SMTP-port engedélyezve van a helyi virtuálisgép-tűzfalon és a virtuális gép hálózati biztonsági csoportján keresztül.

## <a name="next-steps"></a>Következő lépések
Az automatizált Backup v2 a felügyelt biztonsági mentést konfigurálja az Azure-beli virtuális gépeken. Ezért fontos, hogy [áttekintse a felügyelt biztonsági mentés dokumentációját](https://msdn.microsoft.com/library/dn449496.aspx) a viselkedés és a következmények megismerése érdekében.

A következő cikkben további biztonsági mentési és visszaállítási útmutatást talál az Azure-beli virtuális gépek SQL Serverához: [SQL Server Azure-beli Virtual Machines biztonsági mentése és visszaállítása](virtual-machines-windows-sql-backup-recovery.md).

További információ az egyéb rendelkezésre álló automatizálási feladatokról: [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további információkért lásd: [SQL Server az azure Virtual Machines áttekintése](virtual-machines-windows-sql-server-iaas-overview.md).

