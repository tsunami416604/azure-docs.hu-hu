---
title: SQL Server-adatbázis áttelepítése SQL Server kiszolgálóra virtuális gépen | Microsoft dokumentumok
description: Megtudhatja, hogyan telepítheti át a helyszíni felhasználói adatbázist az SQL Serverbe egy Azure virtuális gépen.
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
ms.openlocfilehash: c8314b04c05e2ecba2715b807171b5c1a2fa988a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646863"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>SQL Server-adatbázis áttelepítése Azure-beli virtuális gépen futó SQL Serverre

Számos módszer van egy helyszíni SQL Server felhasználói adatbázis áttelepítésére egy Azure-beli virtuális gép SQL Server szolgáltatásába. Ez a cikk röviden megvitatja a különböző módszereket, és a legjobb módszert ajánlja a különböző forgatókönyvekhez.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > Az SQL Server 2008 és az SQL Server 2008 R2 a helyszíni példányok [támogatási életciklusának végéhez](https://www.microsoft.com/sql-server/sql-server-2008) közeledik. A támogatás kiterjesztéséhez áttelepítheti az SQL Server-példányt egy Azure virtuális gépre, vagy megvásárolhatja a kiterjesztett biztonsági frissítéseket, hogy a helyszíni maradjon. További információ: [Az SQL Server 2008 és 2008 R2 támogatásának kiterjesztése az Azure-ral](virtual-machines-windows-sql-server-2008-eos-extend-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Melyek az elsődleges áttelepítési módszerek?
Az elsődleges áttelepítési módszerek a következők:

* Helyszíni biztonsági mentés végrehajtása tömörítéssel és a biztonsági másolat fájljának manuális másolása az Azure virtuális gépre
* Biztonsági mentés végrehajtása az URL-címről, és visszaállítás az Azure virtuális gépére az URL-címről
* Válassza le, majd másolja az adatokat és a naplófájlokat az Azure blobstorage-ba, majd csatolja az SQL Serverhez az Azure VM-ben az URL-címről
* A helyszíni fizikai gép átalakítása Hyper-V vHD-vé, feltöltés az Azure Blob storage-ba, majd üzembe helyezés új virtuális gépként a feltöltött virtuális merevlemez használatával
* Merevlemez szállítása a Windows importálási/exportálási szolgáltatásával
* Ha a helyszíni AlwaysOn rendelkezésre állási csoport központi telepítésével rendelkezik, az [Azure-replikának hozzáadása varázslóval](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) hozzon létre egy replikát az Azure-ban, majd feladatátvételt, és a felhasználókat az Azure-adatbázis-példányra irányítsa.
* Az SQL Server [tranzakciós replikációjával](https://msdn.microsoft.com/library/ms151176.aspx) konfigurálhatja az Azure SQL Server-példányt előfizetőként, majd letilthatja a replikációt, és a felhasználókat az Azure-adatbázispéldányra irányíthatja

> [!TIP]
> Ugyanezeket a technikákat is használhatja adatbázisok áthelyezéséhez az SQL Server virtuális gépei között az Azure-ban. Például nincs támogatott módja az SQL Server-gallery-image VM egyik verzióról/kiadásról a másikra történő frissítésére. Ebben az esetben hozzon létre egy új SQL Server virtuális gép az új verzió/kiadás, majd használja az egyik az áttelepítési technikák ebben a cikkben az adatbázisok áthelyezéséhez. 

## <a name="choosing-your-migration-method"></a>Az áttelepítési módszer kiválasztása
Az optimális adatátviteli teljesítmény érdekében az adatbázisfájlokáttelepítést az Azure virtuális gépegy tömörített biztonsági másolat használatával.

Az adatbázis-áttelepítési folyamat során az állásidő minimalizálásához használja az AlwaysOn vagy a transactional replication kapcsolót.

Ha a fenti módszerek nem használhatók, manuálisan telepítse át az adatbázist. Ezzel a módszerrel általában egy adatbázis biztonsági mentést, majd egy másolatot az adatbázis biztonsági mentésaz Azure-ba, majd egy adatbázis-visszaállítás. Az adatbázisfájlokat maguk is másolhatja az Azure-ba, majd csatolhatja őket. Számos módszer, amellyel elvégezheti ezt a manuális folyamatot egy adatbázis áttelepítése egy Azure virtuális gép.

> [!NOTE]
> Amikor az SQL Server 2014 vagy az SQL Server 2016 régebbi verzióiról frissít, érdemes megfontolnia, hogy szükség van-e módosításokra. Azt javasoljuk, hogy az áttelepítési projekt részeként kezelje az SQL Server új verziója által nem támogatott szolgáltatásoktól való összes függőséget. A támogatott kiadásokról és forgatókönyvekről a [Frissítés az SQL Server kiszolgálóra](https://msdn.microsoft.com/library/bb677622.aspx)című témakörben talál további információt.

Az alábbi táblázat felsorolja az egyes elsődleges áttelepítési módszereket, és bemutatja, hogy az egyes módszerek használata mikor a legmegfelelőbb.

| Módszer | Forrásadatbázis verziója | Céladatbázis verziója | Forrásadatbázis biztonsági másolatának méretmegkötése | Megjegyzések |
| --- | --- | --- | --- | --- |
| [Helyszíni biztonsági mentés végrehajtása tömörítéssel és a biztonsági másolat fájljának manuális másolása az Azure virtuális gépre](#backup-and-restore) |SQL Server 2005 vagy nagyobb |SQL Server 2005 vagy nagyobb |[Az Azure VM tárolási korlátja](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) | Ez egy nagyon egyszerű és jól bevált technika adatbázisok gépek közötti mozgatásához. |
| [Biztonsági mentés végrehajtása az URL-címről, és visszaállítás az Azure virtuális gépére az URL-címről](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 vagy nagyobb |SQL Server 2012 SP1 CU2 vagy nagyobb |< 12,8 TB az SQL Server 2016-hoz, egyébként 1 TB < | Ez a módszer csak egy másik módja a biztonsági másolat fájl áthelyezésének a virtuális gépaz Azure storage használatával. |
| [Válassza le, majd másolja az adatokat és a naplófájlokat az Azure blob storage-ba, majd csatolja az SQL Serverhez az Azure virtuális gépében az URL-címről](#detach-and-attach-from-url) |SQL Server 2005 vagy nagyobb |SQL Server 2014 vagy nagyobb |[Az Azure VM tárolási korlátja](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Akkor használja ezt a módszert, ha [ezeket a fájlokat az Azure Blob storage szolgáltatás használatával](https://msdn.microsoft.com/library/dn385720.aspx) szeretné tárolni, és csatolja őket az Azure Virtuális gépen futó SQL Server-hez, különösen a nagyon nagy adatbázisok esetén. |
| [A helyszíni gép átalakítása Hyper-V vHD-kké, feltöltés az Azure Blob storage-ba, majd üzembe helyezegy új virtuális gépet a feltöltött virtuális merevlemez használatával](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 vagy nagyobb |SQL Server 2005 vagy nagyobb |[Az Azure VM tárolási korlátja](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |A [saját SQL Server-licenc létrehozásakor](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)használható, ha az SQL Server egy régebbi verzióján futtatott adatbázist telepít át, vagy ha a rendszer- és felhasználói adatbázisokat együtt, más felhasználói adatbázisoktól és/vagy rendszeradatbázisoktól függő adatbázis áttelepítésének részeként telepíti át. |
| [Merevlemez szállítása a Windows importálási/exportálási szolgáltatásával](#ship-hard-drive) |SQL Server 2005 vagy nagyobb |SQL Server 2005 vagy nagyobb |[Az Azure VM tárolási korlátja](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |A [Windows importálási/exportálási szolgáltatás](../../../storage/common/storage-import-export-service.md) használata, ha a manuális másolási módszer túl lassú, például nagyon nagy adatbázisok esetén |
| [Az Azure Replica hozzáadása varázsló használata](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |SQL Server 2012 vagy nagyobb |SQL Server 2012 vagy nagyobb |[Az Azure VM tárolási korlátja](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Minimalizálja az állásidőt, akkor használható, ha mindig helyszíni telepítéssel rendelkezik |
| [SQL Server tranzakciós replikáció használata](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 vagy nagyobb |SQL Server 2005 vagy nagyobb |[Az Azure VM tárolási korlátja](https://azure.microsoft.com/documentation/articles/azure-resource-manager/management/azure-subscription-service-limits/) |Akkor használható, ha minimálisra kell csökkentenie az állásidőt, és nincs mindig helyszíni telepítése |

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás
Készítsen biztonsági másolatot az adatbázisról tömörítéssel, másolja a biztonsági másolatot a virtuális gépre, majd állítsa vissza az adatbázist. Ha a biztonságimásolat-fájl nagyobb, mint 1 TB, csíkot kell, hogy csíkot, mert a virtuális gép lemezének maximális mérete 1 TB. A felhasználói adatbázis manuális áttelepítéséhez kövesse az alábbi általános lépéseket:

1. Teljes adatbázis-biztonsági mentés végrehajtása a helyszíni helyre.
2. Hozzon létre vagy töltsön fel egy virtuális gépet az SQL Server kívánt verziójával.
3. Állítsa be a kapcsolatot az Ön igényei nek megfelelően. Lásd: [Csatlakozás SQL Server virtuális géphez az Azure-ban (Resource Manager).](virtual-machines-windows-sql-connect.md)
4. Másolja a biztonsági másolat fájl(oka)t a virtuális gépre a távoli asztal, a Windows Intéző vagy a másolás i parancs segítségével a parancssorból.

## <a name="backup-to-url-and-restore"></a>Biztonsági mentés URL-címre és visszaállítás
Ahelyett, hogy biztonsági mentést egy helyi fájlba, használhatja a [biztonsági másolatot AZ URL-címet,](https://msdn.microsoft.com/library/dn435916.aspx) és majd állítsa vissza az URL-címről a virtuális gépre. Az SQL Server 2016-ban a csíkozott biztonsági mentési készletek támogatottak, teljesítményajánlott, és szükséges, hogy túllépjék a méretkorlátok blobonként. Nagyon nagy adatbázisok esetén a [Windows importálási/exportálási szolgáltatás](../../../storage/common/storage-import-export-service.md) használata ajánlott.

## <a name="detach-and-attach-from-url"></a>LEválasztás és csatolás az URL-címről
Válassza le az adatbázist és a naplófájlokat, és vigye át őket az [Azure Blob storage-ba.](https://msdn.microsoft.com/library/dn385720.aspx) Ezután csatolja az adatbázist az Azure virtuális gép URL-címéről. Ezt akkor használja, ha azt szeretné, hogy a fizikai adatbázisfájlok a Blob storage-ban legyenek. Ez nagyon nagy adatbázisok esetén hasznos lehet. A felhasználói adatbázis manuális áttelepítéséhez kövesse az alábbi általános lépéseket:

1. Válassza le az adatbázisfájlokat a helyszíni adatbázispéldányról.
2. Másolja a leválasztott adatbázisfájlokat az Azure blob storage-ba az [AZCopy parancssori segédprogrammal.](../../../storage/common/storage-use-azcopy.md)
3. Csatolja az adatbázisfájlokat az Azure URL-címaz SQL Server-példány az Azure virtuális gép.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Konvertálás virtuális gépre, feltöltés a megadott URL-címre, majd üzembe helyezés új virtuális gépként
Ezzel a módszerrel áttelepítheti az összes rendszer- és felhasználói adatbázist egy helyszíni SQL Server-példányban az Azure virtuális gépre. A következő általános lépésekkel telepítsen át egy teljes SQL Server-példányt ezzel a manuális módszerrel:

1. Fizikai vagy virtuális gépek átalakítása Hyper-V VD-kké.
2. VHD-fájlok feltöltése az Azure [Storage-ba az Add-AzureVHD parancsmag](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx)használatával.
3. Telepítsen egy új virtuális gépet a feltöltött virtuális merevlemez használatával.

> [!NOTE]
> Egy teljes alkalmazás áttelepítéséhez fontolja meg az [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)használatát].

## <a name="ship-hard-drive"></a>Hajó merevlemez
A [Windows importálási/exportálási szolgáltatás módszerrel](../../../storage/common/storage-import-export-service.md) nagy mennyiségű fájladatokat vihet át az Azure Blob storage-ba olyan helyzetekben, amikor a hálózaton keresztüli feltöltés megfizethetetlenül költséges vagy nem megvalósítható. Ezzel a szolgáltatással egy vagy több merevlemezt küld, amely tartalmazza ezeket az adatokat egy Azure-adatközpontba, ahol az adatok at feltölti a tárfiókba.

## <a name="next-steps"></a>További lépések
Az SQL Server Azure virtuális gépeken való futtatásáról az [SQL Server azure-beli virtuális gépek – áttekintés című témakörben olvashat bővebben.](virtual-machines-windows-sql-server-iaas-overview.md)

> [!TIP]
> Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

Az Azure SQL Server virtuális gép rögzített lemezképből történő létrehozásáról a CSS SQL Server Engineers blogján található Tippek & trükkök az [Azure SQL virtuális gépek "klónozásával" kapcsolatban](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) című témakörben olvashat.

