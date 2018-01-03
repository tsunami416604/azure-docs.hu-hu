---
title: "Automatikus biztonsági mentés az SQL Server virtuális gépek (klasszikus) |} Microsoft Docs"
description: "Ismerteti az automatikus biztonsági mentés szolgáltatás az SQL Server fut, az Azure virtuális gépek erőforrás-kezelő használatával. "
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 3333e830-8a60-42f5-9f44-8e02e9868d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.openlocfilehash: f7664291c2f45c422d52f682d08dbb67ab32b099
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Automatikus biztonsági mentés az SQL Server Azure virtuális gépekben (klasszikus)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-automated-backup.md)
> * [Klasszikus](../classic/sql-automated-backup.md)
> 
> 

Automatikus biztonsági mentés automatikusan konfigurálja a [Microsoft Azure Backup felügyelt](https://msdn.microsoft.com/library/dn449496.aspx) az összes meglévő és új adatbázis egy SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális gépen. Ez lehetővé teszi a tartós Azure blob-tárolók rendszeres biztonsági konfigurálása. Automatikus biztonsági mentés függ a [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](../classic/sql-server-agent-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ez a cikk erőforrás-kezelő verziójának megtekintése: [automatikus biztonsági mentés az SQL Server az Azure virtuális gépek erőforrás-kezelő](../sql/virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Előfeltételek
Automatikus biztonsági mentés használja, fontolja meg a következő előfeltételek teljesülését:

**Operációs rendszer**:

* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

**SQL Server verziójához/kiadásához**:

* Az SQL Server 2014 Standard
* SQL Server 2014 Enterprise

> [!NOTE]
> SQL Server 2016 jelenleg nem támogatott az automatikus biztonsági mentés.
> 
> 

**Adatbázis-konfiguráció**:

* Cél adatbázisok kell megadni a teljes helyreállítási modell.

**Az Azure PowerShell**:

* [Telepítse a legújabb Azure PowerShell-parancsok](/powershell/azure/overview).

**SQL Server IaaS bővítmény**:

* [Az SQL Server IaaS-bővítményének telepítése](../classic/sql-server-agent-extension.md).

## <a name="settings"></a>Beállítások
A következő táblázat ismerteti a beállításokat, amelyek az automatikus biztonsági mentés. Klasszikus virtuális gépeket a beállítások konfigurálásához PowerShell kell használnia.

| Beállítás | Tartomány (alapértelmezett) | Leírás |
| --- | --- | --- |
| **Automatikus biztonsági mentés** |Engedélyezi/letiltja (letiltva) |Engedélyezi vagy letiltja az automatikus biztonsági mentés az SQL Server 2014 Standard vagy Enterprise rendszert futtató Azure virtuális gép esetében. |
| **Megőrzési időtartam** |1-30 nap (30 nap) |A biztonsági másolatot a megőrizni kívánt napok száma. |
| **Tárfiók** |Azure storage-fiók (a storage-fiók létrehozása a megadott virtuális gép) |Azure-tárfiók a blob Storage tárolóban végzett tárolása automatikus biztonsági mentés fájlok használatára. Egy tároló összes biztonsági mentési fájlok tárolására szolgáló ezen a helyen jön létre. A biztonságimásolat-fájl elnevezési tartalmazza a dátum, idő és a gép nevét. |
| **Titkosítás** |Engedélyezi/letiltja (letiltva) |Engedélyezi vagy letiltja a titkosítást. Ha titkosítás engedélyezve van, a biztonsági másolat visszaállítása a tanúsítványok találhatók a megadott tárfiókhoz a ugyanazon automaticbackup tárolóban, az azonos elnevezési konvenció. Ha a jelszó is módosul, egy új tanúsítványt hoz létre, hogy a jelszó, de a régi tanúsítvány marad a korábbi biztonsági másolatok. |
| **Jelszó** |Jelszó szöveg (nincs) |A titkosítási kulcsok jelszava. Erre csak akkor van szükség, ha engedélyezve van-e a titkosítás. Titkosított biztonsági másolat visszaállítása a helyes jelszót és a kapcsolódó kerül a biztonsági mentés idején használt tanúsítvány kell lennie. | **Biztonsági mentési rendszer adatbázisok** | Engedélyezi/letiltja (letiltva) | A Master, Model és MSDB teljes biztonsági másolatok készítése |
| **Konfigurálja a biztonsági mentés ütemezése** | Manuális vagy automatikus (automatikus) | Válassza ki **automatikus** számára automatikusan teljes és a naplófájl biztonsági mentések napló növekedési alapján. Válassza ki **manuális** számára adja meg a teljes ütemezését, valamint naplófájl-biztonsági mentések. |

## <a name="configuration-with-powershell"></a>PowerShell-konfiguráció
A következő PowerShell-példa automatikus biztonsági mentés meglévő SQL Server 2014 virtuális van beállítva. A **New-AzureVMSqlServerAutoBackupConfig** parancs biztonsági másolatokat tárolni a $storageaccount változó által megadott Azure storage-fiók automatikus biztonsági mentés beállításait konfigurálja. Ezek a biztonsági másolatok 10 napig lesznek megőrizve. A **Set-AzureVMSqlServerExtension** parancs frissíti a megadott Azure virtuális gép ezekkel a beállításokkal.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Eltarthat néhány percig, telepítése és konfigurálása az SQL Server IaaS-ügynök.

Titkosítás engedélyezéséhez módosítsa a előző parancsfájlt a EnableEncryption paramétert, és egy jelszót a CertificatePassword paraméter (biztonságos karakterlánc). A következő parancsfájl lehetővé teszi, hogy az előző példában az automatikus biztonsági mentés beállításait, és hozzáadja a titkosítás.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Az automatikus biztonsági mentés letiltásához futtassa ugyanazt a parancsfájlt nélkül a **-engedélyezése** paramétert a **New-AzureVMSqlServerAutoBackupConfig**. Csakúgy, mint a telepítés, tiltsa le az automatikus biztonsági mentés több percet is igénybe vehet.

> [!NOTE]
> Letiltása, és az SQL Server IaaS-ügynök eltávolítása nem távolítja el a korábban konfigurált való felügyelt biztonsági mentésének beállításait. Automatikus biztonsági mentés letiltása, vagy az SQL Server IaaS-ügynök eltávolítása előtt tiltsa le.
> 
> 

## <a name="next-steps"></a>További lépések
Automatikus biztonsági mentés való felügyelt biztonsági mentésének konfigurálása Azure virtuális gépeken. Ezért fontos, hogy [tekintse át a felügyelt biztonsági mentésének dokumentációjában](https://msdn.microsoft.com/library/dn449496.aspx) a viselkedést és alkalmazásainak megértéséhez.

További biztonsági mentés található, és állítsa vissza a következő témakörben található útmutatót az SQL Server Azure virtuális gépeken: [biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépek](../sql/virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Más elérhető automation feladatokkal kapcsolatos további információkért lásd: [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](../classic/sql-server-agent-extension.md).

További információ az Azure virtuális gépeken futó SQL Server: [SQL Server Azure virtuális gépek – áttekintés](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

