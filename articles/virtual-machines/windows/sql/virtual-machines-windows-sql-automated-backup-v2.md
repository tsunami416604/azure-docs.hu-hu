---
title: "Az SQL Server 2016 Azure virtuális gépek biztonsági mentési v2 automatikus |} Microsoft Docs"
description: "Ismerteti az automatikus biztonsági mentés szolgáltatás az SQL Server 2016 rendszert futtató virtuális gépek Azure-ban. Ez a cikk csak a virtuális gépeknek a Resource Manager használatával."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: ebd23868-821c-475b-b867-06d4a2e310c7
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/05/2017
ms.author: jroth
ms.openlocfilehash: e7e14b0243f82c672392d5ab4bb6aca01156465b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="automated-backup-v2-for-sql-server-2016-azure-virtual-machines-resource-manager"></a>Automatikus biztonsági mentési v2 az SQL Server 2016 az Azure virtuális gépek (erőforrás-kezelő)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016](virtual-machines-windows-sql-automated-backup-v2.md)

Automatikus biztonsági mentés v2 automatikusan konfigurálja a [Microsoft Azure Backup felügyelt](https://msdn.microsoft.com/library/dn449496.aspx) az összes meglévő és új adatbázis egy Azure virtuális gépen futó SQL Server 2016 Standard, Enterprise vagy fejlesztői kiadását. Ez lehetővé teszi a tartós Azure blob-tárolók rendszeres biztonsági konfigurálása. Az automatikus biztonsági mentés v2 függ a [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Automatikus biztonsági mentés v2 használ, tekintse át a következő előfeltételeket:

**Operációs rendszer**:

- Windows Server 2012 R2
- Windows Server 2016

**SQL Server verziójához/kiadásához**:

- SQL Server 2016 Standard
- SQL Server 2016 Enterprise
- SQL Server 2016 Developer

> [!IMPORTANT]
> Az automatikus biztonsági mentés v2 SQL Server 2016 működik. Ha SQL Server 2014-et használ, automatikus biztonsági mentés v1 segítségével készítsen biztonsági másolatot az adatbázisról. További információkért lásd: [automatikus biztonsági mentés az SQL Server 2014 Azure virtuális gépek](virtual-machines-windows-sql-automated-backup.md).

**Adatbázis-konfiguráció**:

- Cél adatbázisok kell megadni a teljes helyreállítási modell. További információ arról, hogyan befolyásolják a teljes helyreállítási modell biztonsági mentések,: [biztonsági mentés alatt a teljes helyreállítási modell](https://technet.microsoft.com/library/ms190217.aspx).
- Rendszer-adatbázisokat nem rendelkeznek a teljes helyreállítási modell használatára. Napló-biztonságimásolatokat a modell vagy MSDB végrehajtását írja elő, ha a teljes helyreállítási modell kell használnia.
- Az alapértelmezett SQL Server-példány céladatbázisokhoz kell lennie. Az SQL Server IaaS bővítmény nem támogatja az elnevezett példányok.

**Az Azure-alapú üzemi modell**:

- Resource Manager

> [!NOTE]
> Automatikus biztonsági mentés támaszkodik a **SQL Server infrastruktúra-szolgáltatási ügynök bővítmény**. A gyűjtemény lemezképei aktuális SQL virtuális gép alapértelmezés szerint adja hozzá ezt a bővítményt. További információkért lásd: [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások
A következő táblázat ismerteti a beállítások automatikus biztonsági mentés 2-es verzió konfigurálható. A tényleges konfigurációs lépései eltérőek attól függően, hogy használ-e az Azure portálon vagy az Azure PowerShell-parancsokkal.

### <a name="basic-settings"></a>Alapbeállítások

| Beállítás | Tartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Automatikus biztonsági mentés** | Engedélyezi/letiltja (letiltva) | Engedélyezi vagy letiltja az automatikus biztonsági mentés az SQL Server 2016 Standard vagy Enterprise rendszert futtató Azure virtuális gép esetében. |
| **Megőrzési időtartam** | 1-30 nap (30 nap) | A biztonsági mentések megőrzési napok száma. |
| **Tárfiók** | Azure Storage-fiók | Azure-tárfiók a blob Storage tárolóban végzett tárolása automatikus biztonsági mentés fájlok használatára. Egy tároló összes biztonsági mentési fájlok tárolására szolgáló ezen a helyen jön létre. A biztonságimásolat-fájl elnevezési tartalmazza a dátum, idő és adatbázis-GUID. |
| **Titkosítás** |Engedélyezi/letiltja (letiltva) | Engedélyezi vagy letiltja a titkosítást. Ha engedélyezve van, a biztonsági másolat visszaállítása a tanúsítványok találhatók-e a megadott tárfiók ugyanazon **automaticbackup** az azonos elnevezési konvenció tároló. Ha a jelszó is módosul, egy új tanúsítványt hoz létre, hogy a jelszó, de a régi tanúsítvány marad a korábbi biztonsági másolatok. |
| **Jelszó** |Jelszó szöveg | A titkosítási kulcsok jelszava. Erre csak akkor van szükség, ha engedélyezve van-e a titkosítás. Titkosított biztonsági másolat visszaállítása a helyes jelszót és a kapcsolódó kerül a biztonsági mentés idején használt tanúsítvány kell lennie. |

### <a name="advanced-settings"></a>Speciális beállítások

| Beállítás | Tartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Rendszer-adatbázis biztonsági mentése** | Engedélyezi/letiltja (letiltva) | Ha engedélyezve van, ez a funkció is készít biztonsági másolatot a rendszer-adatbázisokat: Master, MSDB és modell. Az msdb adatbázisban és modell adatbázisok esetén ellenőrizze, hogy azok teljes körű helyreállítási módban Ha azt szeretné, hogy a Naplók biztonsági másolatainak kell venni. Napló-biztonságimásolatokat a rendszer soha nem főkiszolgáló hajtja végre. És a TempDB nem készült biztonsági másolat készül. |
| **Biztonsági mentés ütemezése** | Manuális vagy automatikus (automatikus) | Alapértelmezés szerint a biztonsági mentés ütemezése lesz automatikusan alapján határozza meg a napló növekedési. Manuális biztonsági mentés ütemezése a felhasználó adja meg a biztonsági mentés ideje ablakot. Ebben az esetben biztonsági másolatok csak történik a megadott gyakorisággal, a megadott időszakban egy adott nap. |
| **Teljes biztonsági mentés gyakorisága** | Napi vagy heti | Teljes biztonsági mentések gyakoriságát. Mindkét esetben a teljes biztonsági mentés a következő ütemezett időszak alatt megkezdődik. Heti kiválasztásakor a biztonsági mentések sikerült span több napon belül végre az összes adatbázis sikeresen biztonsági másolatából. |
| **Teljes biztonsági mentés kezdési ideje** | 00:00 – 23:00 (01:00) | Kezdési időpont egy adott nap során, ami teljes biztonsági mentés akkor kerül sor. |
| **Teljes biztonsági mentési időablak** | 1 – 23 óra (1 óra) | A megadott nap során, ami teljes biztonsági mentés akkor kerül sor a időszak időtartama. |
| **Napló biztonsági mentési gyakoriság** | 5 – 60 perc (60 perc) | A napló biztonsági mentések gyakoriságát. |

## <a name="understanding-full-backup-frequency"></a>Teljes biztonsági mentés gyakoriságát ismertetése
Fontos, napi és heti teljes biztonsági mentések közötti különbségek megértése. Ebből a törekvésből végigvezetjük két példaforgatókönyvek keresztül.

### <a name="scenario-1-weekly-backups"></a>1. forgatókönyv: Heti biztonsági mentései
Egy nagyon nagy adatbázisok számát tartalmazó SQL Server virtuális gép van.

Hétfőn, engedélyezze az automatikus biztonsági mentés 2 a következő beállításokkal:

- Biztonsági mentés ütemezése: **manuális**
- Teljes biztonsági mentési gyakoriságot: **heti**
- Teljes biztonsági mentés kezdési ideje: **01:00**
- Teljes biztonsági mentési időablak: **1 óra**

Ez azt jelenti, hogy a következő elérhető biztonsági mentési időszakban kedd 1 óráig 1 órakor. Automatikus biztonsági mentés idő lejárta után megkezdődik egy adatbázis biztonsági másolatának egyszerre. Ebben a forgatókönyvben az adatbázisok kellőképpen hosszúak legyenek, hogy az első néhány adatbázisok teljes biztonsági mentés befejeződik. Azonban egy óra múlva összes az adatbázisok biztonsági mentése volt.

Ez akkor fordul elő, amikor automatikus biztonsági mentés megkezdődik adatbázisainak biztonsági mentése a fennmaradó a következő napon, szerda 13: 00 1 óra a következő. Ha nem minden adatbázis biztonsági mentése volt, hogy időben, azt megpróbál egy időben újra a következő napon. Továbbiakban ez fog folytatódni mindaddig, amíg az összes adatbázis sikeresen nincs biztonsági másolata.

Ha ismét eléri kedd, automatikus biztonsági mentés megkezdődik a adatbázisainak biztonsági mentése az összes még egyszer.

Ebből a forgatókönyvből megtudhatja, hogy az automatikus biztonsági mentés csak a megadott időszak belül fog működni, és az egyes adatbázisok készül biztonsági másolat hetente egyszer. Ez is mutatja, hogy a biztonsági másolatok számára több napon abban az esetben, ahol nincs lehetőség egy nap az összes biztonsági mentés végrehajtásához.

### <a name="scenario-2-daily-backups"></a>2. forgatókönyv: Napi biztonsági mentései
Egy nagyon nagy adatbázisok számát tartalmazó SQL Server virtuális gép van.

Hétfőn, engedélyezze az automatikus biztonsági mentés 2 a következő beállításokkal:

- Biztonsági mentés ütemezése: manuális
- Teljes biztonsági mentési gyakoriságot: naponta
- Teljes biztonsági mentés kezdési ideje: 22:00
- Teljes biztonsági mentési időablak: 6 óra

Ez azt jelenti, hogy a következő elérhető biztonsági mentési időszakban hétfő 10 du. hat órán át. Automatikus biztonsági mentés idő lejárta után megkezdődik egy adatbázis biztonsági másolatának egyszerre.

Ezt követően 10 hat órán át keddjén összes adatbázis teljes biztonsági mentés újra elindul.

> [!IMPORTANT]
> Napi biztonsági mentés ütemezése, ajánlott úgy ütemezni a széles időkerete annak érdekében, hogy minden adatbázisok biztonsági másolat készíthető az időben. Ez különösen fontos abban az esetben, melyekben nagy mennyiségű adatok biztonsági mentéséhez.

## <a name="configuration-in-the-portal"></a>A portál konfigurálása

Az Azure portál segítségével konfigurálja automatikus biztonsági mentés v2 kiépítése során, vagy meglévő SQL Server 2016 virtuális gépek.

### <a name="new-vms"></a>Új virtuális gépek

Az Azure portál segítségével konfigurálhatja az automatikus biztonsági mentés v2, egy új SQL Server 2016-virtuális gép létrehozásakor a Resource Manager üzembe helyezési modellben. 

Az a **SQL Server-beállítások** panelen válassza **automatikus biztonsági mentés**. Az alábbi az Azure portál képernyőképe látható a **SQL automatikus biztonsági mentés** panelen.

![SQL automatikus biztonsági mentés konfigurálása az Azure portálon](./media/virtual-machines-windows-sql-automated-backup-v2/automated-backup-blade.png)

> [!NOTE]
> Az automatikus biztonsági mentés v2 alapértelmezés szerint le van tiltva.

A környezetben, tekintse meg a teljes témakör [az Azure SQL Server virtuális gépek kiépítése](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Meglévő virtuális gépek

Meglévő SQL Server virtuális gépek válassza ki az SQL Server virtuális gépet. Válassza ki a **SQL Server-konfigurációs** szakasza a **beállítások** panelen.

![SQL automatikus biztonsági mentés a meglévő virtuális gépek](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration.png)

Az a **SQL Server-konfigurációs** panelen kattintson a **szerkesztése** gomb az automatikus biztonsági mentési szakaszban.

![Meglévő virtuális gépek SQL automatikus biztonsági mentés konfigurálása](./media/virtual-machines-windows-sql-automated-backup-v2/sql-server-configuration-edit.png)

Ha elkészült, kattintson a **OK** gomb alján a **SQL Server-konfigurációs** panelt, és menti a módosításokat.

Ha első alkalommal engedélyezi automatikus biztonsági mentés, Azure konfigurálja az SQL Server IaaS-ügynök a háttérben. Ebben az időszakban az Azure-portálon előfordulhat, hogy jelenjen meg, hogy az automatikus biztonsági mentés konfigurálva van-e. Várjon egy pár percet, az ügynök telepítve, konfigurálva. Ezt követően az Azure-portálon az új beállítások fogja tartalmazni.

## <a name="configuration-with-powershell"></a>PowerShell-konfiguráció

PowerShell segítségével konfigurálhatja az automatikus biztonsági mentés v2. Mielőtt elkezdené, a következőket kell tennie:

- [Töltse le és telepítse a legújabb Azure PowerShell](http://aka.ms/webpi-azps).
- Nyissa meg a Windows Powershellt, és rendelje hozzá azt a fiókot. Ehhez a lépések a [az előfizetés konfigurálása](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-ps-sql-create#configure-your-subscription) a kiépítési témakör szakaszát.

### <a name="install-the-sql-iaas-extension"></a>Az SQL IaaS-bővítményének telepítése
Kiépített Azure-portálról egy SQL Server virtuális gép, ha az SQL Server IaaS bővítmény már telepítve kell lennie. Segítségével meghatározhatja, hogy ha az telepítve van a virtuális gép meghívásával **Get-AzureRmVM** parancs és megvizsgálta a **bővítmények** tulajdonság.

```powershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"

(Get-AzureRmVM -Name $vmname -ResourceGroupName $resourcegroupname).Extensions 
```

Ha az SQL Server IaaS-ügynök bővítmény telepítve van, megjelenik az "SqlIaaSAgent" vagy "SQLIaaSExtension" jelenik. **ProvisioningState** számára a kiterjesztést is kell megjelenítése "Sikeres". 

Ha van telepítve, vagy nem telepítése sikertelen volt, a következő paranccsal telepítheti. A virtuális gép nevét és az erőforrás csoporton felül meg kell adnia a régióban (**$region**), amely a virtuális gép található.

```powershell
$region = “EASTUS2”
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region 
```

### <a id="verifysettings"></a>Aktuális beállításainak ellenőrzése
Ha engedélyezte az automatikus biztonsági mentés kiépítése során, a PowerShell használatával ellenőrizze az aktuális konfigurációját. Futtassa a **Get-AzureRmVMSqlServerExtension** parancsot, és vizsgálja meg a **AutoBackupSettings** tulajdonság:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Kimeneti kell kapnia a következőhöz hasonló:

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

Ha a kimeneti azt mutatja, hogy **engedélyezése** értékre van állítva **hamis**, akkor el kell automatikus biztonsági mentés engedélyezése. Jó hírünk engedélyezése és konfigurálása az automatikus biztonsági mentés azonos módon. Ezt az információt a következő részben olvashat.

> [!NOTE] 
> A módosítások elvégzése után azonnal ellenőrizze a beállításait, akkor lehetséges, hogy kap vissza a régi konfigurációs értékeket. Várjon néhány percet, és ellenőrizze a beállításait, ismét, és győződjön meg arról, hogy a módosítások alkalmazása megtörtént.

### <a name="configure-automated-backup-v2"></a>Az automatikus biztonsági mentési v2 konfigurálása
PowerShell segítségével engedélyezheti a automatikus biztonsági mentés, valamint hogy a konfiguráció és működés bármikor módosíthatja. 

Először is válassza ki, vagy hozzon létre egy tárfiókot, a biztonságimásolat-fájlok. A következő parancsfájl választja ki egy tárfiókot, vagy hoz létre, ha nem létezik.

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
> Automatikus biztonsági mentés nem támogatja a prémium szintű storage tárolni a biztonsági mentések, de a prémium szintű Storage használó virtuális gépek lemezei készített biztonsági másolat is igénybe vehet.

Ezután a **New-AzureRmVMSqlServerAutoBackupConfig** parancs futtatásával engedélyezze és konfigurálja az automatikus biztonsági mentés v2 biztonsági másolatok tárolhatók az Azure storage-fiók. Ebben a példában a biztonsági mentések őrzi meg a 10 napos vannak állítva. Rendszer-adatbázis biztonsági másolatait engedélyezve vannak. Teljes biztonsági mentések ütemezett heti 20:00 két órán keresztül kezdési idő ablakot. 30 percenként naplóalapú biztonsági mentések ütemezett. A második parancs **Set-AzureRmVMSqlServerExtension**, ezekkel a beállításokkal a megadott Azure virtuális Gépet frissíti.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname 
```

Eltarthat néhány percig, telepítése és konfigurálása az SQL Server IaaS-ügynök. 

Titkosítás engedélyezéséhez módosítsa a felelt meg az előző parancsfájl a **EnableEncryption** paraméterrel együtt (biztonságos karakterlánc) jelszavát a **CertificatePassword** paraméter. A következő parancsfájl lehetővé teszi, hogy az előző példában az automatikus biztonsági mentés beállításait, és hozzáadja a titkosítás.

```powershell
$password = "P@ssw0rd"
$encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  

$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -EnableEncryption -CertificatePassword $encryptionpassword `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType Manual -FullBackupFrequency Weekly `
    -FullBackupStartHour 20 -FullBackupWindowInHours 2 `
    -LogBackupFrequencyInMinutes 30 

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Ellenőrizze a beállításokat alkalmazzák, [az automatikus biztonsági mentés konfigurációjának ellenőrzése](#verifysettings).

### <a name="disable-automated-backup"></a>Automatikus biztonsági mentés tiltása
Automatikus biztonsági mentés letiltásához futtassa ugyanazt a parancsfájlt nélkül a **-engedélyezése** paramétert a **New-AzureRmVMSqlServerAutoBackupConfig** parancsot. Hiányában a **-engedélyezése** paraméter jelzi a parancs a funkció letiltásához. Csakúgy, mint a telepítés, tiltsa le az automatikus biztonsági mentés több percet is igénybe vehet.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>A példaként megadott parancsfájlt
A következő parancsfájl biztosít változók testre szabható engedélyezése és konfigurálása az automatikus biztonsági mentés a virtuális gép számára. Az Ön esetében szükség lehet a követelmények alapján a parancsfájl testreszabása. Például kellene módosításokat, ha letiltja a rendszer adatbázisok biztonsági mentését, vagy engedélyezheti a titkosítást.

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
    -ResourceGroupName $storage_resourcegroupname -BackupSystemDbs `
    -BackupScheduleType $backupscheduletype -FullBackupFrequency $fullbackupfrequency `
    -FullBackupStartHour $fullbackupstarthour -FullBackupWindowInHours $fullbackupwindow `
    -LogBackupFrequencyInMinutes $logbackupfrequency

# Apply the Automated Backup settings to the VM

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>További lépések
Az automatikus biztonsági mentés v2 való felügyelt biztonsági mentésének konfigurálása Azure virtuális gépeken. Ezért fontos, hogy [tekintse át a felügyelt biztonsági mentésének dokumentációjában](https://msdn.microsoft.com/library/dn449496.aspx) a viselkedést és alkalmazásainak megértéséhez.

További biztonsági mentés található, és állítsa vissza a következő témakörben található útmutatót az SQL Server Azure virtuális gépeken: [biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-backup-recovery.md).

Más elérhető automation feladatokkal kapcsolatos további információkért lásd: [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

További információ az Azure virtuális gépeken futó SQL Server: [SQL Server Azure virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

