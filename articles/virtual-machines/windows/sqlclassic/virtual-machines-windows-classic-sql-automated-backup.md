---
title: Automatikus biztonsági mentése az SQL Server virtuális gépek (klasszikus) |} A Microsoft Docs
description: 'Ismerteti az automatikus biztonsági mentés szolgáltatás az SQL Server Azure Virtual Machines szolgáltatásban futó Resource Manager használatával. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3aba118354c51285d714bb127e6f5984f8a50057
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54329752"
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatikus biztonsági mentés az SQL Server Azure-beli virtuális gépeken (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Klasszikus](../classic/sql-automated-backup.md)
> 
> 

Automatikus biztonsági mentés automatikusan konfigurálja a [Managed Backup a Microsoft Azure-bA](https://msdn.microsoft.com/library/dn449496.aspx) az összes meglévő és új adatbázis egy SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális gépen. Ez lehetővé teszi, hogy az adatbázis rendszeres biztonsági mentések, amelyek ténylegesen használják a tartós az Azure blob storage-bA konfigurálása. Automatikus biztonsági mentés függ a [SQL Server IaaS-ügynök bővítmény](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ez a cikk Resource Managerre vonatkozó verziójának megtekintéséhez lásd: [automatikus biztonsági mentés az SQL Server az Azure Virtual Machines Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Előfeltételek
Automatikus biztonsági mentés használ, fontolja meg a következő előfeltételek vonatkoznak:

**Operációs rendszer**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**Az SQL Server-verzióval vagy-kiadással**:

* SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> Automatikus biztonsági mentés az SQL Server 2016 Resource Manager virtuális gépeken támogatott. További információkért lásd: [automatikus biztonsági mentés v2 az SQL Server 2016 az Azure Virtual Machines (Resource Manager)](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup-v2).

**Adatbázis-konfiguráció**:

* Céladatbázis a teljes helyreállítási modellt kell használnia.

**Azure PowerShell**:

* [Telepítse a legújabb Azure PowerShell-parancsok](/powershell/azure/overview).

**SQL Server IaaS Extension**:

* [Telepítse az SQL Server IaaS-bővítményt](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások
A következő táblázat ismerteti a beállításokat, amelyeket az automatikus biztonsági mentés állítható be. Klasszikus virtuális gépeket, a PowerShell kell használnia, ezek a beállítások konfigurálása.

| Beállítás | Címtartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Automatikus biztonsági mentés** |Engedélyezés/letiltás (letiltva) |Engedélyezi vagy letiltja az automatikus biztonsági mentés az SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális gép. |
| **Megőrzési idő** |1 – 30 napig (30 nap) |A biztonsági másolatok megőrzése (nap) száma. |
| **Tárfiók** |Az Azure storage-fiók (a storage-fiók létrehozása a megadott virtuális gép) |Blob storage-ban automatikus biztonsági mentés fájlok tárolására szolgáló Azure storage-fiókkal. Ezen a helyen tárolja az összes biztonsági mentési fájlokat tároló jön létre. Elnevezési szabálya a biztonságimásolat-fájl tartalmazza a dátum, idő és a gép neve. |
| **Titkosítás** |Engedélyezés/letiltás (letiltva) |Engedélyezi vagy letiltja a titkosítást. Ha engedélyezve van a titkosítás, a biztonsági mentés visszaállításához használt tanúsítványok találhatók a megadott tárfiók ugyanabban a tárolóban automaticbackup ugyanazt az elnevezési konvenciót használ. A jelszó is módosul, ha egy új tanúsítvány hozza létre ezt a jelszót, de a régi tanúsítvány továbbra is a korábbi biztonsági másolatok visszaállításához. |
| **Jelszó** |Jelszó szöveg (nincs) |A titkosítási kulcsok jelszava. Ez csak akkor szükséges, ha engedélyezve van-e a titkosítás. Annak érdekében, hogy a titkosított biztonsági másolat visszaállítása, rendelkeznie kell a helyes jelszót és a kapcsolódó az időben, a biztonsági mentéshez használt tanúsítvány. | **Rendszeradatbázisok biztonsági mentése** | Engedélyezés/letiltás (letiltva) | A Master, Model és MSDB teljes biztonsági másolat készítése |
| **Biztonsági mentés ütemezésének konfigurálása** | Manuális vagy automatikus (automatikus) | Válassza ki **automatikus** számára automatikusan teljes és a napló növekedése alapján biztonsági másolataihoz. Válassza ki **manuális** , adja meg a teljes ütemezését és biztonsági másolataihoz. |

## <a name="configuration-with-powershell"></a>PowerShell-konfiguráció
A következő PowerShell-példa az automatikus biztonsági mentés konfigurálva van egy meglévő SQL Server 2014 rendszerű virtuális gép. A **New-AzureVMSqlServerAutoBackupConfig** parancsot a $storageaccount változó által megadott Azure storage-fiókban lévő biztonsági másolatok tárolására automatikus biztonsági mentés beállításait konfigurálja. Ezeket a biztonsági másolatokat 10 napig lesznek megőrizve. A **Set-AzureVMSqlServerExtension** parancs frissíti a megadott Azure virtuális gép ezekkel a beállításokkal.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Telepítse és konfigurálja az SQL Server IaaS-ügynök több percet igénybe vehet.

Titkosítás engedélyezéséhez módosítsa az előző parancsfájlt és egy jelszót (biztonságos karakterlánc) CertificatePassword paraméter EnableEncryption paraméter átadására. A következő parancsfájl lehetővé teszi, hogy az előző példában az automatikus biztonsági mentés beállításait, és hozzáadja a titkosítás.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Automatikus biztonsági mentés letiltása, futtassa ugyanezt a szkriptet nélkül a **-engedélyezése** paramétert a **New-AzureVMSqlServerAutoBackupConfig**. Csakúgy, mint a telepítés, automatikus biztonsági mentés letiltása több percig is eltarthat.

> [!NOTE]
> Letiltás és az SQL Server IaaS-ügynök eltávolítása nem távolítja el a korábban konfigurált felügyelt biztonsági mentési beállítások. Automatikus biztonsági mentés letiltása, vagy az SQL Server IaaS-ügynök eltávolítása előtt tiltsa le.
> 
> 

## <a name="next-steps"></a>További lépések
Automatikus biztonsági mentés konfigurálása Azure virtuális gépek felügyelt biztonsági mentési. Ezért fontos, hogy [felügyelt biztonsági mentés a dokumentációban](https://msdn.microsoft.com/library/dn449496.aspx) viselkedését és következményeiről.

Keresse meg a további biztonsági mentést, és állítsa vissza az SQL Server Azure virtuális gépeken a következő témakörben található útmutatást: [Biztonsági mentés és visszaállítás Azure-beli virtuális gépeken az SQL Serverhez](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Más elérhető automation-feladatokkal kapcsolatos további információkért lásd: [SQL Server IaaS-ügynök bővítmény](../classic/sql-server-agent-extension.md).

További információ az Azure virtuális gépeken futó SQL Server: [SQL Server on Azure Virtual Machines – áttekintés](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

