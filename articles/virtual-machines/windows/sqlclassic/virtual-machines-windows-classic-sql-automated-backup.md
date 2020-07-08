---
title: SQL Server Virtual Machines automatikus biztonsági mentése (klasszikus) | Microsoft Docs
description: 'Az Azure Virtual Machines-ben futtatott SQL Server automatizált biztonsági mentési funkciója a Resource Manager használatával. '
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
ms.openlocfilehash: c792b217f49121b6d3d6eaf2d8f8380997683bd8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84014672"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>SQL Server automatikus biztonsági mentése az Azure Virtual Machines (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)
> * [Klasszikus](../classic/sql-automated-backup.md)
> 
> 

Az automatikus biztonsági mentés automatikusan [úgy konfigurálja a felügyelt biztonsági mentést, hogy](https://msdn.microsoft.com/library/dn449496.aspx) az Azure-beli virtuális gépen lévő összes meglévő és új adatbázisra Microsoft Azure a SQL Server 2014 standard vagy Enterprise rendszert futtató Azure Ez lehetővé teszi, hogy az Azure Blob Storage-t használó normál adatbázis-biztonsági mentéseket konfigurálja. Az automatikus biztonsági mentés a [SQL Server IaaS-ügynök bővítménytől](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)függ.

> [!IMPORTANT] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembe helyezési modell használatát ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. A cikk Resource Manager-verziójának megtekintéséhez lásd: a [SQL Server automatikus biztonsági mentése az Azure Virtual Machines Resource Managerben](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md).

## <a name="prerequisites"></a>Előfeltételek
Az automatikus biztonsági mentés használatához vegye figyelembe a következő előfeltételeket:

**Operációs rendszer**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server verzió/kiadás**:

* SQL Server 2014 standard
* SQL Server 2014 Enterprise

> [!NOTE]
> A SQL Server 2016 automatizált biztonsági mentése a Resource Manager virtuális gépeken támogatott. További információ: [SQL Server 2016 Azure Virtual Machines (Resource Manager) automatizált Backup v2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Adatbázis-konfiguráció**:

* A célként megadott adatbázisoknak a teljes helyreállítási modellt kell használniuk.

**Azure PowerShell**:

* [Telepítse a legújabb Azure PowerShell-parancsokat](/powershell/azure/overview).

**SQL Server IaaS-bővítmény**:

* [Telepítse a SQL Server IaaS bővítményt](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások
Az alábbi táblázat az automatikus biztonsági mentéshez konfigurálható beállításokat ismerteti. A klasszikus virtuális gépek esetében a PowerShell használatával kell konfigurálni ezeket a beállításokat.

| Beállítás | Tartomány (alapértelmezett) | Description |
| --- | --- | --- |
| **Automatikus biztonsági mentés** |Engedélyezés/letiltás (letiltva) |Engedélyezheti vagy letilthatja a SQL Server 2014 standard vagy Enterprise rendszert futtató Azure-beli virtuális gépek automatizált biztonsági mentését. |
| **Megtartási időszak** |1-30 nap (30 nap) |A biztonsági másolat megőrzésének napjainak száma. |
| **Tárfiók** |Azure Storage-fiók (a megadott virtuális géphez létrehozott Storage-fiók) |Egy Azure Storage-fiók, amelyet a blob Storage-ban lévő automatizált biztonságimásolat-fájlok tárolására használ. Ezen a helyen létrejön egy tároló az összes biztonságimásolat-fájl tárolásához. A biztonságimásolat-fájl elnevezési konvenciója tartalmazza a dátumot, az időt és a gép nevét. |
| **Titkosítás** |Engedélyezés/letiltás (letiltva) |Engedélyezheti vagy letilthatja a titkosítást. Ha engedélyezve van a titkosítás, a biztonsági mentés visszaállításához használt tanúsítványok az azonos elnevezési konvenciót használó azonos automaticbackup-tárolóban található megadott Storage-fiókban találhatók. Ha a jelszó megváltozik, új tanúsítvány jön létre ezzel a jelszóval, de a régi tanúsítvány továbbra is a korábbi biztonsági mentéseket állítja vissza. |
| **Jelszó** |Jelszó szövege (nincs) |A titkosítási kulcsok jelszava. Erre csak akkor van szükség, ha engedélyezve van a titkosítás. A titkosított biztonsági mentés visszaállításához a biztonsági másolat készítésének időpontjában használt jelszóval és kapcsolódó tanúsítvánnyal kell rendelkeznie. |
| **Rendszeradatbázisok biztonsági mentése** | Engedélyezés/letiltás (letiltva) | Teljes biztonsági másolatok készítése a Master, a Model és a MSDB |
| **Biztonsági mentések ütemezésének konfigurálása** | Manuális/automatizált (automatizált) | Az **automatizált** lehetőség kiválasztásával automatikusan elvégezheti a biztonsági mentést, és naplózhatja a naplóbeli növekedést. Válassza a **manuális** lehetőséget a teljes és naplózott biztonsági másolatok ütemezésének megadásához. |

## <a name="configuration-with-powershell"></a>Konfigurálás a PowerShell-lel
A következő PowerShell-példában az automatikus biztonsági mentés egy meglévő SQL Server 2014 virtuális gépre van konfigurálva. A **New-AzureVMSqlServerAutoBackupConfig** parancs az automatikus biztonsági mentési beállításokat konfigurálja úgy, hogy az $storageaccount változó által megadott Azure Storage-fiókban tárolja a biztonsági másolatokat. Ezeket a biztonsági mentéseket 10 napig őrzi meg a rendszer. A **set-AzureVMSqlServerExtension** parancs frissíti a megadott Azure-beli virtuális gépet ezekkel a beállításokkal.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

A SQL Server IaaS-ügynök telepítése és konfigurálása több percet is igénybe vehet.

A titkosítás engedélyezéséhez módosítsa az előző szkriptet, hogy átadja a EnableEncryption paramétert a CertificatePassword paraméterhez tartozó jelszóval (Secure string) együtt. A következő parancsfájl lehetővé teszi az előző példában szereplő automatizált biztonsági mentési beállításokat, és titkosítja a titkosítást.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Az automatikus biztonsági mentés letiltásához futtassa ugyanazt a parancsfájlt anélkül, hogy a **-enable** paramétert a **New-AzureVMSqlServerAutoBackupConfig**. A telepítéshez hasonlóan több percet is igénybe vehet, hogy letiltsa az automatizált biztonsági mentést.

> [!NOTE]
> A SQL Server IaaS-ügynök letiltása és eltávolítása nem távolítja el a korábban konfigurált felügyelt biztonsági mentési beállításokat. Az SQL Server IaaS-ügynök letiltása vagy eltávolítása előtt tiltsa le az automatikus biztonsági mentést.
> 
> 

## <a name="next-steps"></a>További lépések
Az automatikus biztonsági mentés az Azure-beli virtuális gépeken felügyelt biztonsági mentést konfigurál. Ezért fontos, hogy [áttekintse a felügyelt biztonsági mentés dokumentációját](https://msdn.microsoft.com/library/dn449496.aspx) a viselkedés és a következmények megismerése érdekében.

A következő témakörben talál további biztonsági mentési és visszaállítási útmutatást az Azure-beli virtuális gépeken való SQL Serverról: [biztonsági mentés és visszaállítás az SQL Serverhoz az azure Virtual Machinesban](../../../azure-sql/virtual-machines/windows/backup-restore.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

További információ az egyéb rendelkezésre álló automatizálási feladatokról: [SQL Server IaaS-ügynök bővítmény](../classic/sql-server-agent-extension.md).

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további információkért lásd: [SQL Server az azure Virtual Machines áttekintése](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

