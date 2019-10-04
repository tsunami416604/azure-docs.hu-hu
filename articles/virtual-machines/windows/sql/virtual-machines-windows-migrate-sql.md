---
title: SQL Server-adatbázis migrálása egy virtuális gépre SQL Serverba | Microsoft Docs
description: Ismerje meg, hogyan telepíthet át egy helyszíni felhasználói adatbázist egy Azure-beli virtuális gép SQL Server.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.topic: article
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 5a8b66c181505a617b002d1a45675d4677588b1c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102195"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>SQL Server-adatbázis áttelepítése Azure-beli virtuális gépen futó SQL Serverre

A helyszíni SQL Server felhasználói adatbázisok számos módon telepíthetők át egy Azure-beli virtuális gépen SQL Serverba. Ez a cikk röviden bemutatja a különböző módszereket, és javaslatot tesz a legjobb módszerre a különböző forgatókönyvekhez.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > SQL Server 2008 és SQL Server 2008 R2 a helyszíni példányok [támogatási](https://www.microsoft.com/sql-server/sql-server-2008) életciklusának végére közeledik. A támogatás kiterjesztéséhez áttelepítheti SQL Server példányát egy Azure-beli virtuális gépre, vagy megvásárolhatja a kibővített biztonsági frissítéseket, hogy a helyszínen is megmaradjon. További információ: a [2008-es és a 2008 R2-es verzió SQL Server támogatásának kiterjesztése az Azure](virtual-machines-windows-sql-server-2008-eos-extend-support.md) -ban

## <a name="what-are-the-primary-migration-methods"></a>Mik az elsődleges áttelepítési módszerek?
Az elsődleges áttelepítési módszerek a következők:

* Helyszíni biztonsági mentés tömörítéssel és a biztonságimásolat-fájl manuális másolása az Azure-beli virtuális gépre
* Készítsen biztonsági másolatot az URL-címre, és állítsa vissza az Azure-beli virtuális gépre az URL-címről
* Válassza le, majd másolja az adatok és naplófájlok között az Azure Blob Storage-ba, majd csatolja SQL Server az Azure-beli virtuális gépen az URL-címről
* Helyszíni fizikai gép konvertálása Hyper-V VHD-re, feltöltés az Azure Blob Storage-ba, majd üzembe helyezés új virtuális géppel a feltöltött VHD használatával
* Merevlemez-meghajtó kiszállítása a Windows import/export szolgáltatással
* Ha a helyszíni AlwaysOn rendelkezésre állási csoporttal rendelkezik, az Azure- [replika hozzáadása varázslóval](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) hozzon létre egy replikát az Azure-ban, majd a feladatátvételt, majd a felhasználókat az Azure Database-példányra mutatva
* SQL Server [tranzakciós replikálással](https://msdn.microsoft.com/library/ms151176.aspx) konfigurálja az Azure SQL Server-példányt előfizetőként, majd tiltsa le a replikációt, és a felhasználók az Azure Database-példányra mutatnak.

> [!TIP]
> Ugyanezeket a technikákat használhatja az adatbázisok SQL Server virtuális gépek közötti áthelyezéséhez az Azure-ban. Nem támogatott például, hogy egy SQL Server Gallery-rendszerkép virtuális gépet egy verzióról vagy kiadásról egy másikra frissítsen. Ebben az esetben létre kell hoznia egy új SQL Server VMt az új verzióval/kiadással, majd a cikkben ismertetett áttelepítési módszerek egyikének használatával kell áthelyeznie az adatbázisokat. 

## <a name="choosing-your-migration-method"></a>Az áttelepítési módszer kiválasztása
Az optimális adatátviteli teljesítmény érdekében a tömörített biztonságimásolat-fájllal telepítse át az adatbázisfájlok az Azure-beli virtuális gépre.

Az adatbázis-áttelepítési folyamat során az állásidő minimalizálásához használja a AlwaysOn vagy a tranzakciós replikálási lehetőséget.

Ha a fenti metódusok használata nem lehetséges, akkor manuálisan telepítse át az adatbázist. Ezzel a módszerrel általában egy adatbázis biztonsági másolatát kell kezdenie, majd az adatbázis biztonsági másolatát az Azure-ba, majd egy adatbázis-visszaállítást kell végrehajtania. Az adatbázisfájlok saját maguk is másolhatók az Azure-ba, majd csatolhatók. Az adatbázisok Azure-beli virtuális gépekre való áttelepítésének több módja is van.

> [!NOTE]
> Ha a SQL Server régebbi verzióiról SQL Server 2014-re vagy SQL Server 2016-re frissít, érdemes megfontolnia, hogy szükség van-e a módosításokra. Javasoljuk, hogy az áttelepítési projekt részeként a SQL Server új verziója által nem támogatott szolgáltatások összes függőségét foglalkozzon. További információ a támogatott kiadásokról és forgatókönyvekről: [frissítés SQL Serverra](https://msdn.microsoft.com/library/bb677622.aspx).

A következő táblázat felsorolja az egyes elsődleges áttelepítési módszereket, és bemutatja, hogy az egyes módszerek használata a legmegfelelőbb-e.

| Módszer | Forrás adatbázis verziója | Céladatbázis verziója | Forrás-adatbázis biztonsági másolatának mérete korlátozás | Megjegyzések |
| --- | --- | --- | --- | --- |
| [Helyszíni biztonsági mentés tömörítéssel és a biztonságimásolat-fájl manuális másolása az Azure-beli virtuális gépre](#backup-and-restore) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Azure VM-tároló korlátja](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Ez egy nagyon egyszerű és jól tesztelt módszer az adatbázisok számítógépek közötti áthelyezéséhez. |
| [Készítsen biztonsági másolatot az URL-címre, és állítsa vissza az Azure-beli virtuális gépre az URL-címről](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 vagy újabb |SQL Server 2012 SP1 CU2 vagy újabb |< 12,8 TB SQL Server 2016, ellenkező esetben < 1 TB | Ez a módszer csak egy másik módszer a biztonságimásolat-fájl áthelyezésére a virtuális gépre az Azure Storage használatával. |
| [Válassza le, majd másolja az adatok és a naplófájlokat az Azure Blob Storage-ba, majd csatolja SQL Server az Azure-beli virtuális gépen az URL-címről](#detach-and-attach-from-url) |SQL Server 2005 vagy újabb |SQL Server 2014 vagy újabb |[Azure VM-tároló korlátja](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Akkor használja ezt a módszert, ha [ezeket a fájlokat az Azure Blob Storage szolgáltatással tervezi tárolni](https://msdn.microsoft.com/library/dn385720.aspx) , és csatolja őket egy Azure-beli virtuális gépen futó SQL Serverhoz, különösen nagy méretű adatbázisokhoz |
| [Helyszíni gép konvertálása Hyper-V virtuális merevlemezekre, feltöltés az Azure Blob Storage-ba, majd új virtuális gép üzembe helyezése feltöltött VHD használatával](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Azure VM-tároló korlátja](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Akkor használja, ha [saját SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)-licencet használ, amikor olyan adatbázist telepít át, amelyet a SQL Server egy régebbi verzióján fog futtatni, vagy ha a rendszer-és felhasználói adatbázisokat az adatbázis áttelepítésének részeként együtt szeretné áttelepíteni más felhasználói adatbázisokból és/vagy rendszeradatbázisok. |
| [Merevlemez-meghajtó kiszállítása a Windows import/export szolgáltatással](#ship-hard-drive) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Azure VM-tároló korlátja](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |A [Windows import/export szolgáltatás](../../../storage/common/storage-import-export-service.md) használata, ha a manuális másolási módszer túl lassú, például nagyon nagy méretű adatbázisokkal |
| [Az Azure-replika hozzáadása varázsló használata](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 vagy újabb |SQL Server 2012 vagy újabb |[Azure VM-tároló korlátja](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Csökkentse az állásidőt, ha mindig helyszíni üzembe helyezést használ |
| [SQL Server tranzakciós replikáció használata](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 vagy újabb |SQL Server 2005 vagy újabb |[Azure VM-tároló korlátja](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Akkor használja, ha csökkentenie kell az állásidőt, és nincs mindig helyszíni üzembe helyezése |

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás
Készítsen biztonsági másolatot az adatbázisról a tömörítéssel, másolja a biztonsági másolatot a virtuális gépre, majd állítsa vissza az adatbázist. Ha a biztonságimásolat-fájl 1 TB-nál nagyobb, akkor azt le kell készíteni, mert a virtuális gép lemezének maximális mérete 1 TB. A következő általános lépésekkel telepítheti át a felhasználói adatbázisokat a manuális módszer használatával:

1. Teljes adatbázis biztonsági mentése egy helyszíni helyre.
2. Hozzon létre vagy töltsön fel egy virtuális gépet a SQL Server kívánt verziójával.
3. A kapcsolat beállítása a követelmények alapján. Lásd: [kapcsolódás SQL Server virtuális géphez az Azure-ban (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. Másolja a biztonságimásolat-fájl (oka) t a virtuális gépre a távoli asztal, a Windows Intéző vagy egy parancssorból a Másolás parancs használatával.

## <a name="backup-to-url-and-restore"></a>Biztonsági mentés az URL-címre és a visszaállításra
Helyi fájlra történő biztonsági mentés helyett használhatja a [biztonsági mentést az URL-címre](https://msdn.microsoft.com/library/dn435916.aspx) , majd visszaállíthatja a virtuális gépre az URL-címet. A SQL Server 2016-as számú szalagos biztonságimásolat-készlet támogatott, a teljesítményhez ajánlott, és a Blobok méretének meghaladása szükséges. A nagy méretű adatbázisok esetében ajánlott a [Windows import/export szolgáltatás](../../../storage/common/storage-import-export-service.md) használata.

## <a name="detach-and-attach-from-url"></a>Leválasztás és csatolás URL-címről
Válassza le az adatbázist és a naplófájlokat, majd vigye át őket az [Azure Blob Storage](https://msdn.microsoft.com/library/dn385720.aspx)-ba. Ezután csatolja az adatbázist az Azure-beli virtuális gépen lévő URL-címről. Akkor használja ezt a lehetőséget, ha azt szeretné, hogy a fizikai adatbázisfájlok a blob Storage-ban helyezkednek el. Ez nagyon nagy méretű adatbázisok esetében hasznos lehet. A következő általános lépésekkel telepítheti át a felhasználói adatbázisokat a manuális módszer használatával:

1. Válassza le az adatbázisfájlok a helyszíni adatbázis-példányból.
2. Másolja a leválasztott adatbázisfájlok az Azure Blob Storage-ba a [AZCopy parancssori segédprogram](../../../storage/common/storage-use-azcopy.md)használatával.
3. Csatolja az adatbázisfájlok az Azure URL-címről az SQL Server példányhoz az Azure-beli virtuális gépen.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Konvertálás virtuális gépre, feltöltés a megadott URL-címre, majd üzembe helyezés új virtuális gépként
Ezzel a módszerrel telepítheti át az összes rendszer-és felhasználói adatbázist egy helyszíni SQL Server-példányról az Azure-beli virtuális gépre. A következő általános lépésekkel telepítheti át a teljes SQL Server példányt a manuális metódus használatával:

1. Fizikai vagy virtuális gépek átalakítása Hyper-V virtuális merevlemezekre.
2. Töltse fel a VHD-fájlokat az Azure Storage [-ba az Add-AzureVHD parancsmag](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)használatával.
3. Helyezzen üzembe egy új virtuális gépet a feltöltött virtuális merevlemez használatával.

> [!NOTE]
> Egy teljes alkalmazás áttelepíthetők a [Azure site Recovery](../../../site-recovery/site-recovery-overview.md)] használatával.

## <a name="ship-hard-drive"></a>Merevlemez-meghajtó kiszállítása
A [Windows import/export szolgáltatás módszerével](../../../storage/common/storage-import-export-service.md) nagy mennyiségű fájlt vihet át az Azure Blob Storage-ba olyan helyzetekben, amikor a hálózaton keresztüli feltöltés megfizethetetlenül drága vagy nem valósítható meg. Ezzel a szolgáltatással egy vagy több olyan merevlemezt küld egy Azure-adatközpontba, amely tartalmazza az adott adatait, és a rendszer feltölti az adatait a Storage-fiókjába.

## <a name="next-steps"></a>További lépések
További információ az Azure Virtual Machines SQL Server futtatásáról: [SQL Server az Azure-on Virtual Machines áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

Az Azure SQL Server virtuális gépek rögzített lemezképből való létrehozásával kapcsolatos útmutatásért tekintse meg a [tippek & a "klónozás" Azure SQL-alapú virtuális gépeket](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) a CSS SQL Server mérnökök blogján a rögzített lemezképek című cikkben.

