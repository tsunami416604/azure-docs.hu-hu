---
title: "SQL Server-adatbázis áttelepítése SQL Server a virtuális gép |} Microsoft Docs"
description: "Ismerje meg az SQL Server egy helyi felhasználói adatbázis áttelepítése egy Azure virtuális gépen."
services: virtual-machines-windows
documentationcenter: 
author: sabotta
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: carlasab
ms.openlocfilehash: 68767534298783083a441aa295611914d0df9db0
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>SQL Server-adatbázis áttelepítése Azure-beli virtuális gépen futó SQL Serverre

Számos módszer a helyszíni SQL Server felhasználói adatbázis áttelepítése SQL Server egy Azure virtuális gép van. Ez a cikk röviden ismertetik a különböző módszerekről, és a legjobb módszer különböző forgatókönyvek esetén ajánlott.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Mik az elsődleges áttelepítési módszereinek?
Az elsődleges áttelepítési megoldások a következők:

* Tömörítés használatával a helyszíni biztonsági mentés végrehajtásához, és manuálisan másolja a biztonságimásolat-fájl közzététele az Azure virtuális gépen
* URL-cím és az Azure virtuális géphez való visszaállítás az URL-címet a biztonsági mentés
* Válassza le, és ezután a adatainak és naplókönyvtárainak fájlokat másolja az Azure blob storage, majd csatlakoztassa SQL Server Azure virtuális gép URL-címről
* A helyszíni fizikai számítógép átalakítása Hyper-V virtuális merevlemez feltöltése az Azure Blob storage és ezután telepítheti, mint a új virtuális gép használatával feltöltött virtuális merevlemez
* Küldje el a merevlemez-meghajtóról Windows Import/Export szolgáltatás használata
* Ha az AlwaysOn központi telepítése a helyszíni, használja a [replika Azure hozzáadása varázsló](../classic/sql-onprem-availability.md) replikájának létrehozása az Azure és feladatátvételi, mutasson a felhasználók a Azure database-példányt
* SQL Server használata [tranzakciós replikáció](https://msdn.microsoft.com/library/ms151176.aspx) előfizetőként az Azure SQL Server-példány konfigurálását, és tiltsa le a replikációt, mutasson a felhasználók a Azure database-példányt

> [!TIP]
> Ezek a technikák segítségével is adatbázisok áthelyezése az Azure SQL Server virtuális gépek között. Például nincs egy SQL Server galéria-kép VM frissíthet egy verziójához/kiadásához másik támogatott mód. Ebben az esetben meg kell hozzon létre egy új SQL Server virtuális Gépet az új verziójához/kiadásához, és hajtsa végre az áttelepítési technikák ebben a cikkben az adatbázisok áthelyezése. 

## <a name="choosing-your-migration-method"></a>Az áttelepítési módszer kiválasztása
Az optimális adatátvitel alapteljesítményének az Azure virtuális gép tömörített biztonságimásolat-fájl használatával az adatbázisfájlok áttelepíteni.

Az állásidő minimalizálása érdekében az adatbázis áttelepítési folyamat során, vagy az AlwaysOn szolgáltatását, vagy a tranzakciós replikáció lehetőséget használja.

Ha nincs a fenti módszerek használatát, manuálisan telepítse át az adatbázist. Ezzel a módszerrel Ön általában elindítja egy után az adatbázis biztonsági másolatának az Azure-adatbázis biztonsági másolatából, és végezze el egy adatbázis visszaállítása. Szeretne másolni az adatbázisfájlokat magukat az Azure, és csatolja őket. Többféleképpen az adatbázis migrálása az Azure virtuális gép be ehhez a manuális eljáráshoz-megvalósításához.

> [!NOTE]
> Az SQL Server 2014 vagy SQL Server 2016 történő frissítéskor az SQL Server régebbi verzióit, érdemes e módosítások szükségesek. Azt javasoljuk, hogy kezelje-e az áttelepítés projekt részeként az SQL Server új verziója nem támogatott funkciók összes függőségét. A támogatott kiadásainak és forgatókönyvek további információkért lásd: [frissítsen az SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

A következő táblázat felsorolja az egyes elsődleges áttelepítési módszerek és mutatjuk be, amikor az egyes módszerek használata leginkább megfelelő.

| Módszer | Source adatbázis-verzió | Cél adatbázis-verzió | Source adatbázis biztonsági másolatának mérete megkötés | Megjegyzések |
| --- | --- | --- | --- | --- |
| [Tömörítés használatával a helyszíni biztonsági mentés végrehajtásához, és manuálisan másolja a biztonságimásolat-fájl közzététele az Azure virtuális gépen](#backup-and-restore) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Az Azure virtuális gép tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Ez egy olyan nagyon egyszerű és tesztelt eljárás adatbázisok áthelyezése gépek között. |
| [URL-cím és az Azure virtuális géphez való visszaállítás az URL-címet a biztonsági mentés](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 vagy gyorsabb |SQL Server 2012 SP1 CU2 vagy gyorsabb |< SQL Server 2016, egyébként < 1 TB-os 12.8 TB | Ez a módszer egy másik módja a biztonságimásolat-fájl áthelyezése a virtuális Gépet az Azure storage használatával. |
| [Válassza le és majd másolja az Azure blob storage a adatainak és naplókönyvtárainak fájlokat, majd csatlakoztassa SQL Server Azure virtuális gépen az URL-cím](#detach-and-attach-from-url) |SQL Server 2005 vagy újabb |SQL Server 2014 vagy újabb |[Az Azure virtuális gép tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Ezt a módszert használja, ha azt tervezi, hogy [tárolja ezeket a fájlokat az Azure Blob storage szolgáltatással](https://msdn.microsoft.com/library/dn385720.aspx) és csatolja azokat egy Azure virtuális gép, különösen olyan nagyméretű adatbázisok az SQL Serveren |
| [A helyszíni számítógép átalakítása Hyper-V virtuális merevlemezeket, töltse fel az Azure Blob storage és majd a feltöltött virtuális merevlemez használatával új virtuális gép telepítése](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Az Azure virtuális gép tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |A következő esetekben használja [állapotba hozása a saját SQL Server licence](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), az áttelepítés alatt egy adatbázist, amely az SQL Server egy régebbi verzióját futtatja, vagy áttelepítésekor rendszer és a felhasználó adatbázisok együtt az adatbázis függ-e más áttelepítés felhasználói adatbázisokat és/vagy rendszer-adatbázisokat. |
| [Küldje el a merevlemez-meghajtóról Windows Import/Export szolgáltatás használata](#ship-hard-drive) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Az Azure virtuális gép tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Használja a [Windows Import/Export szolgáltatás](../../../storage/common/storage-import-export-service.md) manuális másolásának módszere túl lassú, például a nagyon nagy adatbázisok esetén |
| [Használja az Azure replika varázsló hozzáadása](../classic/sql-onprem-availability.md) |SQL Server 2012-es vagy újabb |SQL Server 2012-es vagy újabb |[Az Azure virtuális gép tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Állásidőt, akkor használja, ha az AlwaysOn helyszíni üzembe helyezéssel rendelkezik |
| [SQL Server tranzakciós replikáció](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Az Azure virtuális gép tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Ha kell, hogy minimalizálják az állásidőt, és nem az AlwaysOn a helyszíni központi telepítése |

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás
Adatbázis biztonsági mentése a tömörítést, a biztonsági másolat a virtuális géphez, majd majd állítsa vissza az adatbázist. Ha a biztonságimásolat-fájl mérete nagyobb, mint 1 TB-os, kell paritásos, mert a Virtuálisgép-lemez maximális mérete 1 TB-os. A következő általános lépések segítségével a manuális módszerrel felhasználói adatbázis áttelepítése:

1. Adatbázis teljes biztonsági másolat készítése a helyszíni helyre.
2. Hozzon létre, vagy töltse fel a szükséges SQL Server-verzió virtuális gépet.
3. A telepítő kapcsolatot a követelmények alapján. Lásd: [csatlakozni egy SQL Server virtuális gépet az Azure (erőforrás-kezelő)](virtual-machines-windows-sql-connect.md).
4. Másolja a biztonságimásolat-készítő (oka) t a virtuális Géphez a távoli asztal, a Windows Explorer vagy a Másolás parancsot a parancssorba.

## <a name="backup-to-url-and-restore"></a>Biztonsági mentés URL-címet, a visszaállítás
Biztonsági mentés egy helyi fájl, akkor helyett használhatja a [URL-cím történő biztonsági mentés](https://msdn.microsoft.com/library/dn435916.aspx) a virtuális Gépet, majd visszaállítja a URL-CÍMRŐL. Az SQL Server 2016 csíkozott biztonságimásolat-készletek támogatottak, ajánlott a teljesítmény, és hosszabb legyen, mint a méretkorlátozásokról / blob szükséges. Nagyon nagy adatbázisok esetén használatát a [Windows Import/Export szolgáltatás](../../../storage/common/storage-import-export-service.md) ajánlott.

## <a name="detach-and-attach-from-url"></a>Válassza le, és csatlakoztassa az URL-címe
Válassza le az adatbázis és naplófájlok fájlokat, és ezeket a [Azure Blob Storage tárolóban](https://msdn.microsoft.com/library/dn385720.aspx). Majd csatolni az adatbázist, az Azure virtuális gépen az URL-címből. Akkor használja, ha azt szeretné, hogy a fizikai adatbázis fájlok tárolását a Blob Storage tárolóban. Ez akkor lehet hasznos, ha nagyon nagy méretű adatbázisokhoz. A következő általános lépések segítségével a manuális módszerrel felhasználói adatbázis áttelepítése:

1. Válassza le a helyszíni adatbázispéldány származó adatbázisfájlok.
2. A leválasztott adatbázisfájlok másolása az Azure blob storage használata a [AZCopy parancssori segédprogram](../../../storage/common/storage-use-azcopy.md).
3. Az adatbázisfájlok csatolása az Azure URL-ről az Azure virtuális gép az SQL Server-példány.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Konvertálás virtuális gépre, feltöltés a megadott URL-címre, majd üzembe helyezés új virtuális gépként
Ezt a módszert a helyszíni SQL Server-példány összes rendszer és a felhasználói adatbázis áttelepítése az Azure virtuális gép. Az alábbi általános lépésekkel végezhető használatával telepítse át a teljes SQL Server-példány a manuális módszerrel:

1. A Hyper-V virtuális merevlemezek fizikai vagy virtuális gépek átalakítása [Microsoft Virtual Machine Converter](https://technet.microsoft.com/library/dn874008(v=ws.11).aspx).
2. VHD-fájlok feltöltése az Azure Storage használatával a [Add-AzureVHD parancsmag](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Új virtuális gép telepítése a feltöltött virtuális merevlemez használatával.

> [!NOTE]
> Áttelepítheti egy teljes alkalmazást, érdemes lehet [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Küldje el a merevlemez-meghajtó
Használja a [Windows Import/Export szolgáltatás metódus](../../../storage/common/storage-import-export-service.md) nagy mennyiségű adatok átvitele Azure Blob Storage tárolóban lévő olyan helyzetekben, ahol a hálózaton keresztül feltöltése elfogadhatatlanul magas vagy nem valósítható meg. Ezzel a szolgáltatással, hogy az adatok egy Azure-adatközpont, amelyen az adatok lesz feltöltve a tárfiókhoz tartalmazó egy vagy több merevlemezek küldése.

## <a name="next-steps"></a>Következő lépések
További információ az SQL Servert futtató Azure virtuális gépeken: [SQL Server Azure virtuális gépek – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

Egy Azure SQL Server virtuális gép létrehozása egy rögzített lemezképből, lásd: [tippek & a "klónozást" Azure SQL-virtuális gépek a rögzített lemezképeket trükkök](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) a CSS SQL Server mérnökök blogjában.

