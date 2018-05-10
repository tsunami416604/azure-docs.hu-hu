---
title: Az SQL Server 2014 Azure virtuális gépek automatikus biztonsági mentés |} Microsoft Docs
description: Ismerteti az automatikus biztonsági mentés szolgáltatás az SQL Server 2014 rendszert futtató virtuális gépek Azure-ban. Ez a cikk csak a virtuális gépeknek a Resource Manager használatával.
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
ms.openlocfilehash: 43ce94653197933a13830003dd07e5b21be2a585
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="automated-backup-for-sql-server-2014-virtual-machines-resource-manager"></a>Automatikus biztonsági mentés SQL Server 2014 virtuális gépek (erőforrás-kezelő)

> [!div class="op_single_selector"]
> * [SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)
> * [SQL Server 2016/2017](virtual-machines-windows-sql-automated-backup-v2.md)

Automatikus biztonsági mentés automatikusan konfigurálja a [Microsoft Azure Backup felügyelt](https://msdn.microsoft.com/library/dn449496.aspx) az összes meglévő és új adatbázis egy SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális gépen. Ez lehetővé teszi a tartós Azure blob-tárolók rendszeres biztonsági konfigurálása. Automatikus biztonsági mentés függ a [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Előfeltételek
Automatikus biztonsági mentés használja, fontolja meg a következő előfeltételek teljesülését:

**Operációs rendszer**:

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

**SQL Server verziójához/kiadásához**:

- SQL Server 2014 Standard
- SQL Server 2014 Enterprise

> [!IMPORTANT]
> Automatikus biztonsági mentés együttműködik az SQL Server 2014. Ha az SQL Server 2016/2017 használ, automatikus biztonsági mentés v2 segítségével készítsen biztonsági másolatot az adatbázisokról. További információkért lásd: [az SQL Server 2016 Azure virtuális gépek automatikus biztonsági mentés v2](virtual-machines-windows-sql-automated-backup-v2.md).

**Adatbázis-konfiguráció**:

- Cél adatbázisok kell megadni a teljes helyreállítási modell. További információ arról, hogyan befolyásolják a teljes helyreállítási modell biztonsági mentések,: [biztonsági mentés alatt a teljes helyreállítási modell](https://technet.microsoft.com/library/ms190217.aspx).
- Az alapértelmezett SQL Server-példány céladatbázisokhoz kell lennie. Az SQL Server IaaS bővítmény nem támogatja az elnevezett példányok.

> [!NOTE]
> Automatikus biztonsági mentés az SQL Server IaaS ügynöke bővítmény támaszkodik. A gyűjtemény lemezképei aktuális SQL virtuális gép alapértelmezés szerint adja hozzá ezt a bővítményt. További információkért lásd: [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások

A következő táblázat ismerteti a beállításokat, amelyek az automatikus biztonsági mentés. A tényleges konfigurációs lépései eltérőek attól függően, hogy használ-e az Azure portálon vagy az Azure PowerShell-parancsokkal.

| Beállítás | Tartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Automatikus biztonsági mentés** | Engedélyezi/letiltja (letiltva) | Engedélyezi vagy letiltja az automatikus biztonsági mentés az SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális gép esetében. |
| **Megőrzési időtartam** | 1-30 nap (30 nap) | A biztonsági másolatot a megőrizni kívánt napok száma. |
| **Tárfiók** | Azure Storage-fiók | Azure-tárfiók a blob Storage tárolóban végzett tárolása automatikus biztonsági mentés fájlok használatára. Egy tároló összes biztonsági mentési fájlok tárolására szolgáló ezen a helyen jön létre. A biztonságimásolat-fájl elnevezési tartalmazza a dátum, idő és a gép nevét. |
| **Titkosítás** | Engedélyezi/letiltja (letiltva) | Engedélyezi vagy letiltja a titkosítást. Ha engedélyezve van, a biztonsági másolat visszaállítása a tanúsítványok találhatók-e a megadott tárfiók ugyanazon `automaticbackup` az azonos elnevezési konvenció tároló. Ha a jelszó is módosul, egy új tanúsítványt hoz létre, hogy a jelszó, de a régi tanúsítvány marad a korábbi biztonsági másolatok. |
| **Jelszó** | Jelszó szöveg | A titkosítási kulcsok jelszava. Erre csak akkor van szükség, ha engedélyezve van-e a titkosítás. Titkosított biztonsági másolat visszaállítása a helyes jelszót és a kapcsolódó kerül a biztonsági mentés idején használt tanúsítvány kell lennie. |

## <a name="configure-in-the-portal"></a>Konfigurálás a portálon

Az Azure portál segítségével automatikus biztonsági mentés konfigurálása a kiépítés során, vagy meglévő SQL Server 2014 virtuális gépek.

## <a name="configure-new-vms"></a>Új virtuális gépek

Az Azure-portál használatával automatikus biztonsági mentés konfigurálása az új SQL Server 2014 virtuális gép létrehozásakor a Resource Manager üzembe helyezési modellben.

Az a **SQL Server-beállítások** ablaktáblán válassza előbb **automatikus biztonsági mentés**. Az alábbi az Azure portál képernyőképe látható a **SQL automatikus biztonsági mentés** beállításait.

![SQL automatikus biztonsági mentés konfigurálása az Azure portálon](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

## <a name="configure-existing-vms"></a>Meglévő virtuális gépek

Meglévő SQL Server virtuális gépek válassza ki az SQL Server virtuális gépet. Válassza ki a **SQL Server-konfigurációs** a virtuális gép szakasza **beállítások**.

![SQL automatikus biztonsági mentés a meglévő virtuális gépek](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

Az a **SQL Server-konfigurációs** ablaktáblán kattintson a **szerkesztése** gomb az automatikus biztonsági mentési szakaszban.

![Meglévő virtuális gépek SQL automatikus biztonsági mentés konfigurálása](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Ha elkészült, kattintson a **OK** gomb alján a **SQL Server-konfigurációs** beállításait a módosítások mentéséhez.

Ha első alkalommal engedélyezi automatikus biztonsági mentés, Azure konfigurálja az SQL Server IaaS-ügynök a háttérben. Ebben az időszakban az Azure-portálon előfordulhat, hogy jelenjen meg, hogy az automatikus biztonsági mentés konfigurálva van-e. Várjon egy pár percet, az ügynök telepítve, konfigurálva. Ezt követően az Azure-portálon az új beállítások fogja tartalmazni.

> [!NOTE]
> Automatikus biztonsági mentés sablon használatával is konfigurálhatja. További információkért lásd: [Azure gyors üzembe helyezés sablon automatikus biztonsági mentés](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configure-with-powershell"></a>Konfigurálja a PowerShell használatával

PowerShell segítségével konfigurálhatja az automatikus biztonsági mentés. Mielőtt elkezdené, a következőket kell tennie:

- [Töltse le és telepítse a legújabb Azure PowerShell](http://aka.ms/webpi-azps).
- Nyissa meg a Windows Powershellt, és rendelje hozzá azt a fiókot a **Connect-AzureRmAccount** parancsot.

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
$region = "EASTUS2"
Set-AzureRmVMSqlServerExtension -VMName $vmname `
    -ResourceGroupName $resourcegroupname -Name "SQLIaasExtension" `
    -Version "1.2" -Location $region
```

> [!IMPORTANT]
> Ha a kiterjesztés már nincs telepítve, a bővítmény telepítése újraindítja az SQL Server szolgáltatást.

### <a id="verifysettings"></a> Aktuális beállításainak ellenőrzése

Ha engedélyezte az automatikus biztonsági mentés kiépítése során, a PowerShell használatával ellenőrizze az aktuális konfigurációját. Futtassa a **Get-AzureRmVMSqlServerExtension** parancsot, és vizsgálja meg a **AutoBackupSettings** tulajdonság:

```powershell
(Get-AzureRmVMSqlServerExtension -VMName $vmname -ResourceGroupName $resourcegroupname).AutoBackupSettings
```

Kimeneti kell kapnia a következőhöz hasonló:

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

Ha a kimeneti azt mutatja, hogy **engedélyezése** értékre van állítva **hamis**, akkor el kell automatikus biztonsági mentés engedélyezése. Jó hírünk engedélyezése és konfigurálása az automatikus biztonsági mentés azonos módon. Ezt az információt a következő részben olvashat.

> [!NOTE] 
> A módosítások elvégzése után azonnal ellenőrizze a beállításait, akkor lehetséges, hogy kap vissza a régi konfigurációs értékeket. Várjon néhány percet, és ellenőrizze a beállításait, ismét, és győződjön meg arról, hogy a módosítások alkalmazása megtörtént.

### <a name="configure-automated-backup"></a>Automatikus biztonsági mentés konfigurálása
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

Ezután a **New-AzureRmVMSqlServerAutoBackupConfig** parancs futtatásával engedélyezze és konfigurálja a biztonsági mentések tárolására az Azure storage-fiók automatikus biztonsági mentés beállításait. Ebben a példában a biztonsági mentések 10 napig maradnak meg. A második parancs **Set-AzureRmVMSqlServerExtension**, ezekkel a beállításokkal a megadott Azure virtuális Gépet frissíti.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -Enable `
    -RetentionPeriodInDays 10 -StorageContext $storage.Context `
    -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

Eltarthat néhány percig, telepítése és konfigurálása az SQL Server IaaS-ügynök.

> [!NOTE]
> Nincsenek más beállítások **New-AzureRmVMSqlServerAutoBackupConfig** , amely csak érvényes SQL Server 2016-os és az automatikus biztonsági mentés 2. Az SQL Server 2014 nem támogatja a következő beállításokat: **BackupSystemDbs**, **BackupScheduleType**, **FullBackupFrequency**, **FullBackupStartHour**, **FullBackupWindowInHours**, és **LogBackupFrequencyInMinutes**. Ha ezeket a beállításokat az SQL Server 2014 virtuális gépeken, nincs hiba, de a beállítások nem érvényben. Ha ezeket a beállításokat SQL Server 2016-os virtuális gépen használni kívánt, lásd: [az SQL Server 2016 Azure virtuális gépek automatikus biztonsági mentés v2](virtual-machines-windows-sql-automated-backup-v2.md).

Titkosítás engedélyezéséhez módosítsa a felelt meg az előző parancsfájl a **EnableEncryption** paraméterrel együtt (biztonságos karakterlánc) jelszavát a **CertificatePassword** paraméter. A következő parancsfájl lehetővé teszi, hogy az előző példában az automatikus biztonsági mentés beállításait, és hozzáadja a titkosítás.

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

Ellenőrizze a beállításokat alkalmazzák, [az automatikus biztonsági mentés konfigurációjának ellenőrzése](#verifysettings).

### <a name="disable-automated-backup"></a>Automatikus biztonsági mentés tiltása

Automatikus biztonsági mentés letiltásához futtassa ugyanazt a parancsfájlt nélkül a **-engedélyezése** paramétert a **New-AzureRmVMSqlServerAutoBackupConfig** parancsot. Hiányában a **-engedélyezése** paraméter jelzi a parancs a funkció letiltásához. Csakúgy, mint a telepítés, tiltsa le az automatikus biztonsági mentés több percet is igénybe vehet.

```powershell
$autobackupconfig = New-AzureRmVMSqlServerAutoBackupConfig -ResourceGroupName $storage_resourcegroupname

Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig `
    -VMName $vmname -ResourceGroupName $resourcegroupname
```

### <a name="example-script"></a>Példaszkript

A következő parancsfájl biztosít változók testre szabható engedélyezése és konfigurálása az automatikus biztonsági mentés a virtuális gép számára. Az Ön esetében szükség lehet a követelmények alapján a parancsfájl testreszabása. Például kellene módosításokat, ha letiltja a rendszer adatbázisok biztonsági mentését, vagy engedélyezheti a titkosítást.

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

Automatikus biztonsági mentés SQL Server 2014 figyelése, hogy két fő lehetőség közül választhat. Automatikus biztonsági mentés az SQL Server felügyelt biztonsági mentés szolgáltatást használja, mert ugyanazok a figyelési módszerek rendszerre egyaránt vonatkoznak.

Először is lekérdezheti az állapotát meghívásával [msdb.smart_admin.sp_get_backup_diagnostics](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-get-backup-diagnostics-transact-sql). Vagy nem kérdezhető le a [msdb.smart_admin.fn_get_health_status](https://docs.microsoft.com/sql/relational-databases/system-functions/managed-backup-fn-get-health-status-transact-sql) táblázat értékű függvényben.

> [!NOTE]
> A felügyelt biztonsági mentéshez az SQL Server 2014 sémája **msdb.smart_admin**. Az SQL Server 2016 ez változott **msdb.managed_backup**, és a referencia-témakörei a újabb sémát használni. SQL Server 2014, továbbra is használja, de a **smart_admin** séma az összes felügyelt biztonsági mentésének objektum.

Egy másik lehetőség egy értesítéseket a beépített az adatbázisbeli levelezés funkciót.

1. Hívja a [msdb.smart_admin.sp_set_parameter](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/managed-backup-sp-set-parameter-transact-sql) tárolt eljárást egy e-mail címet hozzárendelni a **SSMBackup2WANotificationEmailIds** paraméter. 
1. Engedélyezése [SendGrid](../../../sendgrid-dotnet-how-to-send-email.md) az e-mailek küldése az Azure virtuális gépről.
1. Az SMTP-kiszolgáló és a felhasználó nevét segítségével konfigurálja az adatbázisbeli levelezést. Az adatbázisbeli levelezés konfigurálhatja az SQL Server Management Studio vagy a Transact-SQL parancsokkal. További információkért lásd: [az adatbázisbeli levelezés](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail).
1. [Konfigurálja az SQL Server Agent használatára adatbázisbeli levelezés](https://docs.microsoft.com/sql/relational-databases/database-mail/configure-sql-server-agent-mail-to-use-database-mail).
1. Győződjön meg arról, hogy az SMTP-kiszolgáló portja is engedélyezve van a helyi virtuális gép tűzfal és a hálózati biztonsági csoport a virtuális gép számára.

## <a name="next-steps"></a>További lépések

Automatikus biztonsági mentés való felügyelt biztonsági mentésének konfigurálása Azure virtuális gépeken. Ezért fontos, hogy [tekintse át az SQL Server 2014 felügyelt biztonsági dokumentációjában](https://msdn.microsoft.com/library/dn449497(v=sql.120).aspx).

Kereshet további biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépeken a következő cikkben szereplő útmutatást: [biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépek](virtual-machines-windows-sql-backup-recovery.md).

Más elérhető automation feladatokkal kapcsolatos további információkért lásd: [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md).

További információ az Azure virtuális gépeken futó SQL Server: [SQL Server Azure virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).
