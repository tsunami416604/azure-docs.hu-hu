---
title: Automatikus biztonsági mentés az SQL Server 2014 Azure virtuális gépekhez
description: Ez a cikk ismerteti az Azure-ban futó SQL Server 2014 virtuális gépek automatikus biztonsági mentési szolgáltatását. Ez a cikk az Erőforrás-kezelőt használó virtuális gépekre vonatkozik.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: bdc63fd1-db49-4e76-87d5-b5c6a890e53c
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/03/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c7dea85d8de17a0f65e6e73b5b5fbe619d464d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650332"
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatikus biztonsági mentés az SQL Server 2014 virtuális gépekhez (Erőforrás-kezelő)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Az automatikus biztonsági mentés automatikusan konfigurálja a [Felügyelt biztonsági mentést a Microsoft Azure-ba](https://msdn.microsoft.com/library/dn449496.aspx) az összes meglévő és új adatbázishoz egy SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális gépen. Ez lehetővé teszi a rendszeres adatbázis-biztonsági mentések konfigurálását, amelyek tartós Azure blob storage-t használnak. Az automatikus biztonsági mentés az [SQL Server IaaS ügynökbővítménytől](virtual-machines-windows-sql-server-agent-extension.md)függ.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Az automatikus biztonsági mentés használatához vegye figyelembe az alábbi előfeltételeket:

**Operációs rendszer**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server verzió/kiadás:**

- SQL Server 2014 szabvány
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Az automatikus biztonsági mentés együttműködik az SQL Server 2014 rendszerrel. Ha AZ SQL Server 2016/2017-et használja, az Automatikus biztonsági mentés 2-es segítségével biztonsági másolatot készíthet az adatbázisokról. További információ: [Automated Backup v2 for SQL Server 2016 Azure Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md).

**Adatbázis konfigurációja**:

- A céladatbázisoknak a teljes helyreállítási modellt kell használniuk. A teljes helyreállítási modell biztonsági mentésekre gyakorolt hatásáról a [Teljes helyreállítási modell alatti biztonsági mentés című](https://technet.microsoft.com/library/ms190217.aspx)témakörben talál további információt.
- A céladatbázisoknak az alapértelmezett SQL Server-példányon kell lenniük. Az SQL Server IaaS extension nem támogatja az elnevezett példányokat.

> [!NOTE]
> Az automatikus biztonsági mentés az SQL Server IaaS-ügynökbővítményre támaszkodik. Az aktuális SQL virtuálisgép-gyűjtemény képei alapértelmezés szerint hozzáadják ezt a bővítményt. További információt az [SQL Server IaaS Agent Extension című](virtual-machines-windows-sql-server-agent-extension.md)témakörben talál.

## <a name="settings"></a>Beállítások

Az alábbi táblázat az automatikus biztonsági mentéshez konfigurálható beállításokat ismerteti. A tényleges konfigurációs lépések attól függően változnak, hogy az Azure Portalvagy az Azure Windows PowerShell-parancsokat használja.The actual configuration steps vary on whether you use the Azure Portal or Azure Windows PowerShell commands.

| Beállítás | Tartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Automatikus biztonsági mentés** | Engedélyezés/letiltás (letiltva) | Engedélyezi vagy letiltja az automatikus biztonsági mentést egy SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális géphez. |
| **Megtartási időszak** | 1-30 nap (30 nap) | A biztonsági másolat megőrzésére eltelt napok száma. |
| **Tárfiók** | Azure Storage-fiók | Az automatikus biztonsági mentési fájlok blobstorage-ban való tárolására használt Azure-tárfiók. Ezen a helyen egy tároló jön létre az összes biztonsági másolat fájljának tárolására. A biztonságimásolat-fájl elnevezési konvenciója tartalmazza a dátumot, az időt és a számítógép nevét. |
| **Titkosítás** | Engedélyezés/letiltás (letiltva) | Engedélyezi vagy letiltja a titkosítást. Ha a titkosítás engedélyezve van, a biztonsági másolat visszaállításához használt `automaticbackup` tanúsítványok ugyanabban a tárolóban, ugyanabban az elnevezési konvencióban találhatók a megadott tárfiókban. Ha a jelszó megváltozik, a rendszer új tanúsítványt hoz létre ezzel a jelszóval, de a régi tanúsítvány a korábbi biztonsági mentések visszaállításához marad. |
| **Jelszó** | Jelszó szövege | A titkosítási kulcsok jelszava. Ez csak akkor szükséges, ha a titkosítás engedélyezve van. A titkosított biztonsági másolat visszaállításához a biztonsági másolat készítésekor használt helyes jelszóval és kapcsolódó tanúsítvánnyal kell rendelkeznie. |


## <a name="configure-new-vms"></a>Új virtuális gépek konfigurálása

Az Azure Portal használatával konfigurálhatja az automatikus biztonsági mentést, amikor új SQL Server 2014 virtuális gépet hoz létre az Erőforrás-kezelő telepítési modelljében.

Az **SQL Server beállítások** lapján görgessen le az **Automatikus biztonsági mentés** lapra, és válassza az **Engedélyezés**lehetőséget. A következő Azure Portal képernyőkép az **SQL automatikus biztonsági mentés** i beállításait mutatja.

![SQL automatikus biztonsági mentés konfigurációja az Azure Portalon](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Meglévő virtuális gépek konfigurálása

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

A meglévő SQL Server virtuális gépek, engedélyezheti és letilthatja az automatikus biztonsági mentések, módosítsa a megőrzési időszak, adja meg a tárfiókot, és engedélyezze a titkosítást az Azure Portalon. 

Nyissa meg az SQL Server 2014 virtuális gép [SQL virtuális géperőforrását,](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) és válassza a **Biztonsági mentések**lehetőséget. 

![SQL automatikus biztonsági mentés meglévő virtuális gépekhez](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Ha végzett, a módosítások mentéséhez kattintson az **Alkalmaz** gombra a **Biztonsági mentések** lap alján.

Ha első alkalommal engedélyezi az automatikus biztonsági mentést, az Azure a háttérben konfigurálja az SQL Server IaaS-ügynököt. Ez alatt az idő alatt előfordulhat, hogy az Azure Portal nem mutatja, hogy az automatikus biztonsági mentés konfigurálva van. Várjon néhány percet az ügynök telepítésére és konfigurálására. Ezt követően az Azure Portal az új beállításokat fogja tükrözni.

> [!NOTE]
> Az automatikus biztonsági mentés sablon használatával is konfigurálható. További információt az [Automatikus biztonsági mentés Azure gyorsútmutatósablonjában](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update)talál.

## <a name="configure-with-powershell"></a>Konfigurálás a PowerShell használatával

A PowerShell segítségével konfigurálhatja az automatikus biztonsági mentést. Mielőtt elkezdené, a következőket kell tennie:

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

Ha nincs telepítve, vagy nem sikerült kiépíteni, telepítheti a következő paranccsal. A virtuális gép neve és erőforráscsoportja mellett meg kell adnia azt a régiót (**$region),** amelyben a virtuális gép található. Adja meg az SQL Server virtuális gép licenctípusát, és válassza ki a felosztó-ki-ki-kiosztó vagy a saját licencét az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)segítségével. A licenceléssel kapcsolatos további tudnivalókat a [licencelési modell című](virtual-machines-windows-sql-ahb.md)témakörben talál. 

```powershell
New-AzSqlVM  -Name $vmname `
    -ResourceGroupName $resourcegroupname `
    -Location $region -LicenseType <PAYG/AHUB>
```

> [!IMPORTANT]
> Ha a bővítmény még nincs telepítve, a bővítmény telepítése újraindítja az SQL Server szolgáltatást.

### <a name="verify-current-settings"></a><a id="verifysettings"></a>Az aktuális beállítások ellenőrzése

Ha engedélyezte az automatikus biztonsági mentés kiépítése során, a PowerShell segítségével ellenőrizheti az aktuális konfigurációt. Futtassa a **Get-AzVMSqlServerExtension** parancsot, és vizsgálja meg az **AutoBackupSettings** tulajdonságot:

```powershell
(Get-AzVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

A kimenetnek a következőhöz hasonlónak kell lennie:

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

Ha a kimenet azt mutatja, hogy **az Engedélyezés** értéke **Hamis,** akkor engedélyeznie kell az automatikus biztonsági mentést. A jó hír az, hogy az automatikus biztonsági mentést ugyanúgy engedélyezi és konfigurálja. Ezt az információt a következő szakaszban talál.

> [!NOTE] 
> Ha a módosítás után azonnal ellenőrzi a beállításokat, lehetséges, hogy visszakapja a régi konfigurációs értékeket. Várjon néhány percet, és ellenőrizze újra a beállításokat, és győződjön meg arról, hogy a módosítások at alkalmazták.

### <a name="configure-automated-backup"></a>Automatikus biztonsági mentés konfigurálása
A PowerShell segítségével engedélyezheti az automatikus biztonsági mentést, valamint bármikor módosíthatja annak konfigurációját és viselkedését.

Először jelöljön ki vagy hozzon létre egy tárfiókot a biztonsági másolat fájljaihoz. A következő parancsfájl kijelöl egy tárfiókot, vagy létrehozza azt, ha nem létezik.

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

Ezután a **New-AzVMSqlServerAutoBackupConfig** paranccsal engedélyezheti és konfigurálhatja az automatikus biztonsági mentés beállításait az Azure-tárfiók biztonsági másolatainak tárolásához. Ebben a példában a biztonsági mentések 10 napig megmaradnak. A második parancs, **a Set-AzVMSqlServerExtension**frissíti a megadott Azure virtuális gép ezekkel a beállításokkal.

```powershell
$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Az SQL Server IaaS-ügynök telepítése és konfigurálása több percig is eltarthat.

> [!NOTE]
> A **New-AzVMSqlServerAutoBackupConfig** más beállításai is csak az SQL Server 2016-ra és az Automatikus biztonsági mentés 2-re vonatkoznak. Az SQL Server 2014 nem támogatja a következő beállításokat: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**és **LogBackupFrequencyInMinutes**. Ha ezeket a beállításokat egy SQL Server 2014 virtuális gépen próbálja konfigurálni, nincs hiba, de a beállítások nem lesznek alkalmazva. Ha ezeket a beállításokat egy SQL Server 2016 virtuális gépen szeretné használni, olvassa el [az Automatikus biztonsági mentés 2.](virtual-machines-windows-sql-automated-backup-v2.md)

A titkosítás engedélyezéséhez módosítsa az előző parancsfájlt az **EnableEncryption** paraméter és a **CertificatePassword** paraméter jelszava (secure karakterlánc) átadására. A következő parancsfájl engedélyezi az automatikus biztonsági mentés beállításait az előző példában, és titkosítást ad hozzá.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force

$autobackupconfig = New-AzVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

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
    -ResourceGroupName $storage_resourcegroupname

# Apply the Automated Backup settings to the VM

Set-AzVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="monitoring"></a>Figyelés

Az AUTOMATIKUS biztonsági mentés figyeléséhez az SQL Server 2014-en két fő lehetőség közül választhat. Mivel az automatikus biztonsági mentés az SQL Server felügyelt biztonsági mentés szolgáltatást használja, mindkettőre ugyanazok a figyelési technikák vonatkoznak.

Először az [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Vagy lekérdezheti az [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) tábla értékű függvényt.

> [!NOTE]
> Az SQL Server 2014 felügyelt biztonsági mentésének sémája **az msdb.smart_admin**. Az SQL Server 2016-ban ez **az állapot az msdb.managed_backup-ra**változott, és a referenciatémakörök ezt az újabb sémát használják. Az SQL Server 2014-ben azonban továbbra is a **smart_admin** sémát kell használnia az összes felügyelt biztonsági mentési objektumhoz.

Egy másik lehetőség, hogy kihasználja a beépített Database Mail funkciót az értesítésekhez.

1. Hívja fel az [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) tárolt eljárást, és rendeljen e-mail címet az **SSMBackup2WANotificationEmailIds** paraméterhez. 
1. [Engedélyezze a SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) et az Azure virtuális gépről küldött e-mailek küldéséhez.
1. Az Adatbázis-levél konfigurálásához használja az SMTP-kiszolgálót és a felhasználónevet. Az Adatbázis-levelezés konfigurálható az SQL Server Management Studio vagy a Transact-SQL parancsokkal. További információt az [Adatbázis-levelezés című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail)
1. [Az SQL Server Agent konfigurálása az Adatbázis-levelezés használatára.](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail)
1. Ellenőrizze, hogy az SMTP-port engedélyezett-e a helyi virtuális gép tűzfalán és a virtuális gép hálózati biztonsági csoportján keresztül.

## <a name="next-steps"></a>További lépések

Az automatikus biztonsági mentés konfigurálja a felügyelt biztonsági mentést az Azure virtuális gépein. Ezért fontos [áttekinteni a felügyelt biztonsági mentés dokumentációját az SQL Server 2014 rendszeren.](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx)

Az Azure virtuális gépeken futó SQL Server további biztonsági mentési és visszaállítási útmutatásait a következő cikkben találja: [Biztonsági mentés és visszaállítás az SQL Server hez az Azure virtuális gépeken.](virtual-machines-windows-sql-backup-recovery.md)

Az egyéb elérhető automatizálási feladatokról az [SQL Server IaaS Ügynökbővítmény című](virtual-machines-windows-sql-server-agent-extension.md)témakörben talál további információt.

Az SQL Server Azure-beli virtuális gépeken való futtatásáról az [SQL Server azure-beli virtuális gépek – áttekintés című témakörben olvashat bővebben.](virtual-machines-windows-sql-server-iaas-overview.md)
