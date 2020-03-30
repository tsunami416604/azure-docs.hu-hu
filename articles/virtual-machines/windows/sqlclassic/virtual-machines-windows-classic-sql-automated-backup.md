---
title: Automatikus biztonsági mentés SQL Server virtuális gépekhez (Klasszikus) | Microsoft dokumentumok
description: 'A cikk ismerteti az Erőforrás-kezelőt használó Azure virtuális gépeken futó SQL Server automatikus biztonsági mentési szolgáltatását. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 43ff230d4769a23c9007b3da29858d2105366f9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978104"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatikus biztonsági mentés az SQL Server hez az Azure virtuális gépeken (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Klasszikus](../classic/sql-automated-backup.md)
> 
> 

Az automatikus biztonsági mentés automatikusan konfigurálja a [Felügyelt biztonsági mentést a Microsoft Azure-ba](https://msdn.microsoft.com/library/dn449496.aspx) az összes meglévő és új adatbázishoz egy SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális gépen. Ez lehetővé teszi a rendszeres adatbázis-biztonsági mentések konfigurálását, amelyek tartós Azure blob storage-t használnak. Az automatikus biztonsági mentés az [SQL Server IaaS ügynökbővítménytől](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)függ.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelő és a Klasszikus.](../../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembe helyezési modell használatával ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A cikk Resource Manager-verziójának megtekintéséhez olvassa el az [Automatikus biztonsági mentés az SQL Server szolgáltatáshoz az Azure Virtual Machines Resource Manager alkalmazásban című témakört.](../sql/virtual-machines-windows-sql-automated-backup.md)

## <a name="prerequisites"></a>Előfeltételek
Az automatikus biztonsági mentés használatához vegye figyelembe az alábbi előfeltételeket:

**Operációs rendszer**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server verzió/kiadás:**

* SQL Server 2014 szabvány
* SQL Server 2014 Enterprise

> [!NOTE]
> Az SQL Server 2016 automatikus biztonsági mentését az Erőforrás-kezelő virtuális gépei támogatják. További információ: [Automated Backup v2 for SQL Server 2016 Azure Virtual Machines (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Adatbázis konfigurációja**:

* A céladatbázisoknak a teljes helyreállítási modellt kell használniuk.

**Azure PowerShell:**

* [Telepítse a legújabb Azure PowerShell-parancsokat.](/powershell/azure/overview)

**SQL Server IaaS-bővítmény**:

* [Telepítse az SQL Server IaaS bővítményt](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások
Az alábbi táblázat az automatikus biztonsági mentéshez konfigurálható beállításokat ismerteti. Klasszikus virtuális gépek esetén a PowerShell használatával kell konfigurálniezeket a beállításokat.

| Beállítás | Tartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Automatikus biztonsági mentés** |Engedélyezés/letiltás (letiltva) |Engedélyezi vagy letiltja az automatikus biztonsági mentést egy SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális géphez. |
| **Megtartási időszak** |1-30 nap (30 nap) |A biztonsági másolat megőrzésére eltelt napok száma. |
| **Tárfiók** |Azure storage-fiók (a megadott virtuális géphez létrehozott tárfiók) |Az automatikus biztonsági mentési fájlok blobstorage-ban való tárolására használt Azure-tárfiók. Ezen a helyen egy tároló jön létre az összes biztonsági másolat fájljának tárolására. A biztonságimásolat-fájl elnevezési konvenciója tartalmazza a dátumot, az időt és a számítógép nevét. |
| **Titkosítás** |Engedélyezés/letiltás (letiltva) |Engedélyezi vagy letiltja a titkosítást. Ha a titkosítás engedélyezve van, a biztonsági másolat visszaállításához használt tanúsítványok ugyanabban az automatikus biztonsági mentési tárolóban, ugyanabban az elnevezési konvencióban találhatók a megadott tárfiókban. Ha a jelszó megváltozik, a rendszer új tanúsítványt hoz létre ezzel a jelszóval, de a régi tanúsítvány a korábbi biztonsági mentések visszaállításához marad. |
| **Jelszó** |Jelszó szövege (Nincs) |A titkosítási kulcsok jelszava. Ez csak akkor szükséges, ha a titkosítás engedélyezve van. A titkosított biztonsági másolat visszaállításához a biztonsági másolat készítésekor használt helyes jelszóval és kapcsolódó tanúsítvánnyal kell rendelkeznie. |
| **Rendszeradatbázisok biztonsági mentése** | Engedélyezés/letiltás (letiltva) | Teljes biztonsági másolatot készíthet a Master, Model és MSDB-ről |
| **Biztonsági mentések ütemezésének konfigurálása** | Kézi/automatizált (automatizált) | Válassza **az Automatikus** lehetőséget a teljes és a naplónövekedés alapján történő naplózáshoz. Válassza a **Kézi** lehetőséget a teljes és a naplóbiztonsági mentések ütemezésének megadásához. |

## <a name="configuration-with-powershell"></a>Konfiguráció a PowerShell használatával
A következő PowerShell-példában az automatikus biztonsági mentés egy meglévő SQL Server 2014 virtuális géphez van konfigurálva. A **New-AzureVMSqlServerAutoBackupConfig** parancs konfigurálja az automatikus biztonsági mentés beállításait a $storageaccount változó által megadott Azure storage-fiók biztonsági másolatainak tárolására. Ezek a biztonsági mentések 10 napig megmaradnak. A **Set-AzureVMSqlServerExtension** parancs frissíti a megadott Azure virtuális gép ezekkel a beállításokkal.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Az SQL Server IaaS-ügynök telepítése és konfigurálása több percig is eltarthat.

A titkosítás engedélyezéséhez módosítsa az előző parancsfájlt az EnableEncryption paraméter és a CertificatePassword paraméter jelszava (secure karakterlánc) átadására. A következő parancsfájl engedélyezi az automatikus biztonsági mentés beállításait az előző példában, és titkosítást ad hozzá.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Az automatikus biztonsági mentés letiltásához futtassa ugyanazt a parancsfájlt az **-Enable** paraméter nélkül az **Új-AzureVMSqlServerAutoBackupConfig**paraméterhez. A telepítéshez is több percig is eltarthat az automatikus biztonsági mentés letiltása.

> [!NOTE]
> Az SQL Server IaaS-ügynök letiltása és eltávolítása nem távolítja el a korábban konfigurált felügyelt biztonsági mentés beállításait. Az SQL Server IaaS-ügynök letiltása vagy eltávolítása előtt tiltsa le az automatikus biztonsági mentést.
> 
> 

## <a name="next-steps"></a>További lépések
Az automatikus biztonsági mentés konfigurálja a felügyelt biztonsági mentést az Azure virtuális gépein. Ezért fontos, hogy [tekintse át a dokumentációt a felügyelt biztonsági mentés,](https://msdn.microsoft.com/library/dn449496.aspx) hogy megértsék a viselkedést és következményeit.

Az Alábbi témakörben talál további biztonsági mentési és visszaállítási útmutatást az SQL Server hez az Azure virtuális gépeken: [Biztonsági mentés és visszaállítás az SQL Server hez az Azure virtuális gépeken.](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)

Az egyéb elérhető automatizálási feladatokról az [SQL Server IaaS Ügynökbővítmény című](../classic/sql-server-agent-extension.md)témakörben talál további információt.

Az SQL Server Azure-beli virtuális gépeken való futtatásáról az [SQL Server azure-beli virtuális gépek – áttekintés című témakörben olvashat bővebben.](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

