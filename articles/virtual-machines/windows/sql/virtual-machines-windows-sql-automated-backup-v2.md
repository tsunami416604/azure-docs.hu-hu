---
title: Automatikus biztonsági mentési v2 az SQL Server 2016/2017 Azure virtuális gépekhez | Microsoft dokumentumok
description: Ez a cikk az Azure-ban futó SQL Server 2016/2017 virtuális gépek automatikus biztonsági mentési funkcióját ismerteti. Ez a cikk az Erőforrás-kezelőt használó virtuális gépekre vonatkozik.
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
ms.openlocfilehash: 458012982531e228f7c4968f29e79e8b2e29aa48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651431"
---
# <a name="automated-backup-v2-for-azure-virtual-machines-resource-manager"></a>Automatikus biztonsági mentés i V2 az Azure virtuális gépekhez (Erőforrás-kezelő)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Az Automatikus biztonsági mentés v2 automatikusan konfigurálja [a felügyelt biztonsági mentést a Microsoft Azure-ba](https://msdn.microsoft.com/library/dn449496.aspx) az összes meglévő és új adatbázishoz egy SQL Server 2016/2017 Standard, Enterprise vagy Developer kiadást futtató Azure-beli virtuális gépen. Ez lehetővé teszi a rendszeres adatbázis-biztonsági mentések konfigurálását, amelyek tartós Azure blob storage-t használnak. Az Automatikus biztonsági mentés v2 az [SQL Server IaaS Ügynök-bővítménytől](virtual-machines-windows-sql-server-agent-extension.md)függ.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Az Automatikus biztonsági másolat v2 használatához tekintse át az alábbi előfeltételeket:

**Operációs rendszer**:

- Windows Server 2012 R2
- Windows Server 2016

**SQL Server verzió/kiadás:**

- SQL Server 2016: Fejlesztő, standard vagy nagyvállalati verzió
- SQL Server 2017: Fejlesztő, standard vagy nagyvállalati verzió

> [!IMPORTANT]
> Az automatikus biztonsági mentés 2-es verzió az SQL Server 2016-os vagy újabb verzióival működik. Ha AZ SQL Server 2014-et használja, az Automatikus biztonsági mentés 1-es segítségével biztonsági másolatot készíthet az adatbázisokról. További információ: [Automatikus biztonsági mentés az SQL Server 2014 Azure virtuális gépekhez című témakörben.](virtual-machines-windows-sql-automated-backup.md)

**Adatbázis konfigurációja**:

- A céladatbázisoknak a teljes helyreállítási modellt kell használniuk. A teljes helyreállítási modell biztonsági mentésekre gyakorolt hatásáról a [Teljes helyreállítási modell alatti biztonsági mentés című](https://technet.microsoft.com/library/ms190217.aspx)témakörben talál további információt.
- A rendszeradatbázisoknak nem kell teljes helyreállítási modellt használniuk. Ha azonban naplóbiztonsági mentéseket kell készítenie a Modell vagy az MSDB esetében, teljes helyreállítási modellt kell használnia.
- A céladatbázisoknak az alapértelmezett SQL Server-példányon vagy egy [megfelelően telepített](virtual-machines-windows-sql-server-iaas-faq.md#administration) elnevezett példányon kell lenniük. 

> [!NOTE]
> Az automatikus biztonsági mentés az **SQL Server IaaS ügynökbővítményre**támaszkodik. Az aktuális SQL virtuálisgép-gyűjtemény képei alapértelmezés szerint hozzáadják ezt a bővítményt. További információt az [SQL Server IaaS Agent Extension című](virtual-machines-windows-sql-server-agent-extension.md)témakörben talál.

## <a name="settings"></a>Beállítások
Az alábbi táblázat az automatikus biztonsági mentés 2-es eszközéhez konfigurálható beállításokat ismerteti. A tényleges konfigurációs lépések attól függően változnak, hogy az Azure Portalvagy az Azure Windows PowerShell-parancsokat használja.The actual configuration steps vary on whether you use the Azure Portal or Azure Windows PowerShell commands.

### <a name="basic-settings"></a>Alapbeállítások

| Beállítás | Tartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Automatikus biztonsági mentés** | Engedélyezés/letiltás (letiltva) | Engedélyezi vagy letiltja az automatikus biztonsági mentést egy SQL Server 2016/2017 developer, Standard vagy Enterprise rendszert futtató Azure virtuális gép hez. |
| **Megtartási időszak** | 1-30 nap (30 nap) | A biztonsági mentések megőrzésére eltelt napok száma. |
| **Tárfiók** | Azure Storage-fiók | Az automatikus biztonsági mentési fájlok blobstorage-ban való tárolására használt Azure-tárfiók. Ezen a helyen egy tároló jön létre az összes biztonsági másolat fájljának tárolására. A biztonságimásolat-fájl elnevezési konvenciója tartalmazza a dátumot, az időt és az adatbázis GUID azonosítóját. |
| **Titkosítás** |Engedélyezés/letiltás (letiltva) | Engedélyezi vagy letiltja a titkosítást. Ha a titkosítás engedélyezve van, a biztonsági másolat visszaállításához használt tanúsítványok a megadott tárfiókban találhatók. Ugyanazt az **automatikus biztonsági mentési** tárolót használja ugyanazzal az elnevezési konvencióval. Ha a jelszó megváltozik, a rendszer új tanúsítványt hoz létre ezzel a jelszóval, de a régi tanúsítvány a korábbi biztonsági mentések visszaállításához marad. |
| **Jelszó** |Jelszó szövege | A titkosítási kulcsok jelszava. Ez a jelszó csak akkor szükséges, ha a titkosítás engedélyezve van. A titkosított biztonsági másolat visszaállításához a biztonsági másolat készítésekor használt helyes jelszóval és kapcsolódó tanúsítvánnyal kell rendelkeznie. |

### <a name="advanced-settings"></a>Speciális beállítások

| Beállítás | Tartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Rendszeradatbázis biztonsági másolatai** | Engedélyezés/letiltás (letiltva) | Ha engedélyezve van, ez a szolgáltatás a rendszeradatbázisokról is biztonsági másolatot (Master, MSDB) és Model. Az MSDB és a Model adatbázisok esetében ellenőrizze, hogy teljes helyreállítási módban vannak-e, ha naplóbiztonsági mentést szeretne készíteni. A naplóbiztonsági mentések soha nem kerülnek a Mesterre. És nem biztonsági másolatot készít tempdb. |
| **Biztonsági mentés ütemezése** | Kézi/automatizált (automatizált) | Alapértelmezés szerint a biztonsági mentés ütemezése automatikusan a napló növekedése alapján kerül meghatározásra. A manuális biztonsági mentés ütemezése lehetővé teszi a felhasználó számára a biztonsági mentések időablakának megadását. Ebben az esetben a biztonsági mentések csak a megadott gyakorisággal és egy adott nap megadott időablakában történnek. |
| **Teljes biztonsági mentési gyakoriság** | Napi/heti | A teljes biztonsági mentés gyakorisága. Mindkét esetben a teljes biztonsági mentések a következő ütemezett időablakban kezdődnek. Ha a heti beállítás van kiválasztva, a biztonsági mentések több napig is tarthatnak, amíg az összes adatbázisról sikeresen biztonsági mentés készül. |
| **Teljes biztonsági mentés kezdési időpontja** | 00:00 – 23:00 (01:00) | Egy adott nap kezdő időpontja, amely alatt teljes biztonsági mentés rejlehet. |
| **Teljes biztonsági mentési időablak** | 1 – 23 óra (1 óra) | Egy adott nap azon időszakának időtartama, amely alatt teljes biztonsági mentés történhet. |
| **Biztonsági mentés gyakorisága** | 5 – 60 perc (60 perc) | A naplóbiztonsági mentések gyakorisága. |

## <a name="understanding-full-backup-frequency"></a>A teljes biztonsági mentés gyakoriságának ismertetése
Fontos megérteni a napi és heti teljes biztonsági mentések közötti különbséget. Tekintsük a következő két példa forgatókönyvek.

### <a name="scenario-1-weekly-backups"></a>1. forgatókönyv: Heti biztonsági mentések
Van egy SQL Server virtuális gép, amely számos nagy adatbázisokat tartalmaz.

Hétfőn a következő beállításokkal engedélyezi az Automatikus biztonsági mentés 2-es számát:

- Biztonsági mentés ütemezése: **Kézi**
- Teljes biztonsági mentésgyakoriság: **Heti**
- Teljes biztonsági mentés kezdési időpontja: **01:00**
- Teljes biztonsági mentési idő ablak: **1 óra**

Ez azt jelenti, hogy a következő rendelkezésre álló biztonsági mentési ablak kedd 1 óra 1 óra múlva lesz. Ekkor az automatikus biztonsági mentés egyesével megkezdi az adatbázisok biztonsági mentését. Ebben az esetben az adatbázisok elég nagyok ahhoz, hogy az első pár adatbázisok teljes biztonsági mentések befejeződnek. Egy óra elteltével azonban nem minden adatbázisról lett biztonsági másolato.

Amikor ez megtörténik, az automatikus biztonsági mentés a következő napon, szerdán 1 órakor kezdi meg a fennmaradó adatbázisok biztonsági mentését. Ha ez idő alatt nem minden adatbázisról lett biztonsági másolato, akkor másnap, ugyanabban az időben próbálja meg újra. Ez addig folytatódik, amíg az összes adatbázisról sikeresen nem sikerült biztonsági másolatot létrehozni.

Miután ismét eléri a keddet, az automatikus biztonsági mentés ismét megkezdi az összes adatbázis biztonsági mentését.

Ebben a forgatókönyvben azt mutatja, hogy az automatikus biztonsági mentés csak a megadott időablakon belül működik, és minden adatbázis hetente egyszer biztonsági mentésre kerül. Ez azt is mutatja, hogy lehetséges, hogy a biztonsági mentések több napig is átterjednek abban az esetben, ha nem lehet az összes biztonsági mentést egy nap alatt végrehajtani.

### <a name="scenario-2-daily-backups"></a>2. forgatókönyv: Napi biztonsági mentések
Van egy SQL Server virtuális gép, amely számos nagy adatbázisokat tartalmaz.

Hétfőn a következő beállításokkal engedélyezi az Automatikus biztonsági mentés 2-es számát:

- Biztonsági mentés ütemezése: Kézi
- Teljes biztonsági mentési gyakoriság: Napi
- Teljes biztonsági mentés kezdési időpontja: 22:00
- Teljes biztonsági mentési idő ablak: 6 óra

Ez azt jelenti, hogy a következő rendelkezésre álló biztonsági mentési ablak hétfőn 10:00-kor 6 órán keresztül. Ekkor az automatikus biztonsági mentés egyesével megkezdi az adatbázisok biztonsági mentését.

Aztán kedden 10-kor 6 órán keresztül, az összes adatbázis teljes biztonsági mentése újraindul.

> [!IMPORTANT]
> Napi biztonsági mentések ütemezésekénél ajánlott egy széles időkeretet ütemezni annak érdekében, hogy az összes adatbázisról ezen idő alatt biztonsági másolatot lehessen készíteni. Ez különösen fontos abban az esetben, ha nagy mennyiségű adatról kell biztonsági másolatot tartania.

## <a name="configure-new-vms"></a>Új virtuális gépek konfigurálása

Az Azure Portal használatával konfigurálhatja az automatikus biztonsági mentés 2-es teszteszköz-szolgáltatást, amikor új SQL Server 2016-os vagy 2017-es virtuális gépet hoz létre az Erőforrás-kezelő telepítési modelljében.

Az **SQL Server beállításai** lapon válassza az **Engedélyezés** lehetőséget az **Automatikus biztonsági mentés**csoportban. A következő Azure Portal képernyőkép az **SQL automatikus biztonsági mentés** i beállításait mutatja.

![SQL automatikus biztonsági mentés konfigurációja az Azure Portalon](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Az Automatikus biztonsági mentés v2 alapértelmezés szerint le van tiltva.

## <a name="configure-existing-vms"></a>Meglévő virtuális gépek konfigurálása

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Meglévő SQL Server virtuális gépek esetén keresse meg az [SQL virtuális gépek erőforrását,](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) és válassza a **Biztonsági mentések** lehetőséget az automatikus biztonsági mentések konfigurálásához.

![SQL automatikus biztonsági mentés meglévő virtuális gépekhez](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)


Ha végzett, a módosítások mentéséhez kattintson a **Biztonsági másolatbeállítások** lap alján található **Alkalmaz** gombra.

Ha első alkalommal engedélyezi az automatikus biztonsági mentést, az Azure a háttérben konfigurálja az SQL Server IaaS-ügynököt. Ez alatt az idő alatt előfordulhat, hogy az Azure Portal nem mutatja, hogy az automatikus biztonsági mentés konfigurálva van. Várjon néhány percet, amíg az ügynök telepítése konfigurálva van. Ezt követően az Azure Portal az új beállításokat fogja tükrözni.

## <a name="configure-with-powershell"></a>Konfigurálás a PowerShell használatával

A PowerShell segítségével konfigurálhatja az automatikus biztonsági mentés i2-es. Mielőtt elkezdené, a következőket kell tennie:

- [Töltse le és telepítse a legújabb Azure PowerShellt.](https://aka.ms/webpi-azps)
- Nyissa meg a Windows PowerShellt, és társítsa a fiókjához a **Connect-AzAccount** paranccsal.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

### <a name="install-the-sql-iaas-extension"></a>Az SQL IaaS-bővítmény telepítése
Ha az Azure Portalról kiépített egy SQL Server virtuális gépet, az SQL Server IaaS-bővítményt már telepíteni kell. Megállapíthatja, hogy telepítve van-e a virtuális géphez a **Get-AzVM** parancs hívásával és az **Extensions** tulajdonság vizsgálatával.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Ha az SQL Server IaaS Agent bővítmény telepítve van, akkor az "SqlIaaSAgent" vagy "SQLIaaSExtension" néven jelenik meg. A bővítmény **kiépítésének** a "Sikeres" is meg kell jelennie. 

Ha nincs telepítve, vagy nem sikerült kiépíteni, telepítheti a következő paranccsal. A virtuális gép neve és erőforráscsoportja mellett meg kell adnia azt a régiót (**$region),** amelyben a virtuális gép található.

```powershell
$region = "EASTUS2"
Set-AzVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "2.0" -Location $region 
```

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Az aktuális beállítások ellenőrzése
Ha engedélyezte az automatikus biztonsági mentés kiépítése során, a PowerShell segítségével ellenőrizheti az aktuális konfigurációt. Futtassa a **Get-AzVMSqlServerExtension** parancsot, és vizsgálja meg az **AutoBackupSettings** tulajdonságot:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

A kimenetnek a következőhöz hasonlónak kell lennie:

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

Ha a kimenet azt mutatja, hogy **az Engedélyezés** értéke **Hamis,** akkor engedélyeznie kell az automatikus biztonsági mentést. A jó hír az, hogy az automatikus biztonsági mentést ugyanúgy engedélyezi és konfigurálja. Ezt az információt a következő szakaszban talál.

> [!NOTE] 
> Ha a módosítás után azonnal ellenőrzi a beállításokat, lehetséges, hogy visszakapja a régi konfigurációs értékeket. Várjon néhány percet, és ellenőrizze újra a beállításokat, és győződjön meg arról, hogy a módosítások at alkalmazták.

### <a name="configure-automated-backup-v2"></a>Automatikus biztonsági mentés konfigurálása 2- es
A PowerShell segítségével engedélyezheti az automatikus biztonsági mentést, valamint bármikor módosíthatja annak konfigurációját és viselkedését. 

Először jelöljön ki, vagy hozzon létre egy tárfiókot a biztonsági másolat fájljaihoz. A következő parancsfájl kijelöl egy tárfiókot, vagy létrehozza azt, ha nem létezik.

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
> Az automatikus biztonsági mentés nem támogatja a biztonsági mentések prémium szintű tárhelyen való tárolását, de a prémium szintű storage-ot használó virtuálisgép-lemezekről is készíthet biztonsági mentéseket.

Ezután a **New-AzVMSqlServerAutoBackupConfig** paranccsal engedélyezheti és konfigurálhatja az Automatikus biztonsági mentés v2-beállításokat az Azure-tárfiók biztonsági másolatainak tárolásához. Ebben a példában a biztonsági mentések vannak beállítva, hogy 10 napig megmaradnak. A rendszeradatbázis biztonsági másolatai engedélyezve vannak. A teljes biztonsági mentések hetente lesznek ütemezve, két órán keresztül 20:00 órától kezdődő időablakkal. A naplóbiztonsági mentések 30 percenként vannak ütemezve. A második parancs, **a Set-AzVMSqlServerExtension**frissíti a megadott Azure virtuális gép ezekkel a beállításokkal.

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

Az SQL Server IaaS-ügynök telepítése és konfigurálása több percig is eltarthat. 

A titkosítás engedélyezéséhez módosítsa az előző parancsfájlt az **EnableEncryption** paraméter és a **CertificatePassword** paraméter jelszava (secure karakterlánc) átadására. A következő parancsfájl engedélyezi az automatikus biztonsági mentés beállításait az előző példában, és titkosítást ad hozzá.

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

A beállítások alkalmazásának ellenőrzéséhez [ellenőrizze az Automatikus biztonsági mentés konfigurációt.](#verifysettings)

### <a name="disable-automated-backup"></a>Automatikus biztonsági mentés letiltása
Az automatikus biztonsági mentés letiltásához futtassa ugyanazt a parancsfájlt a **-Enable** paraméter nélkül a **New-AzVMSqlServerAutoBackupConfig** parancshoz. A **-Enable** paraméter hiánya jelzi a parancs nak a szolgáltatás letiltását. A telepítéshez is több percig is eltarthat az automatikus biztonsági mentés letiltása.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Példaszkript
A következő parancsfájl olyan változókkészletét tartalmazza, amelyek testreszabása a virtuális gép automatikus biztonsági mentésének engedélyezéséhez és konfigurálásához. Az Ön esetében előfordulhat, hogy testre kell szabnia a parancsfájlt a követelmények nek megfelelően. Például módosításokat kell végrehajtania, ha le szeretné tiltani a rendszeradatbázisok biztonsági másolatát, vagy engedélyezni szeretné a titkosítást.

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
    -Version "2.0" -Location $region

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

Az automatikus biztonsági mentés figyeléséhez az SQL Server 2016/2017 rendszeren két fő lehetőség közül választhat. Mivel az automatikus biztonsági mentés az SQL Server felügyelt biztonsági mentés szolgáltatást használja, mindkettőre ugyanazok a figyelési technikák vonatkoznak.

Először is az állapotot az [msdb.managed_backup.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Vagy lekérdezheti az [msdb.managed_backup.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tábla értékű függvényt.

Egy másik lehetőség, hogy kihasználja a beépített Database Mail funkciót az értesítésekhez.

1. Hívja fel az [msdb.managed_backup.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) tárolt eljárást, és rendeljen egy e-mail címet az **SSMBackup2WANotificationEmailIds** paraméterhez. 
1. [Engedélyezze a SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) et az Azure virtuális gépről küldött e-mailek küldéséhez.
1. Az Adatbázis-levél konfigurálásához használja az SMTP-kiszolgálót és a felhasználónevet. Az Adatbázis-levelezés konfigurálható az SQL Server Management Studio vagy a Transact-SQL parancsokkal. További információt az [Adatbázis-levelezés című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail)
1. [Az SQL Server Agent konfigurálása az Adatbázis-levelezés használatára.](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail)
1. Ellenőrizze, hogy az SMTP-port engedélyezett-e a helyi virtuális gép tűzfalán és a virtuális gép hálózati biztonsági csoportján keresztül.

## <a name="next-steps"></a>További lépések
Az automatikus biztonsági mentés v2 konfigurálja a felügyelt biztonsági mentést az Azure virtuális gépein. Ezért fontos, hogy [tekintse át a dokumentációt a felügyelt biztonsági mentés,](https://msdn.microsoft.com/library/dn449496.aspx) hogy megértsék a viselkedést és következményeit.

Az Azure virtuális gépeken futó SQL Server további biztonsági mentési és visszaállítási útmutatásait a következő cikkben találja: [Biztonsági mentés és visszaállítás az SQL Server hez az Azure virtuális gépeken.](virtual-machines-windows-sql-backup-recovery.md)

Az egyéb elérhető automatizálási feladatokról az [SQL Server IaaS Ügynökbővítmény című](virtual-machines-windows-sql-server-agent-extension.md)témakörben talál további információt.

Az SQL Server Azure-beli virtuális gépeken való futtatásáról az [SQL Server azure-beli virtuális gépek – áttekintés című témakörben olvashat bővebben.](virtual-machines-windows-sql-server-iaas-overview.md)

