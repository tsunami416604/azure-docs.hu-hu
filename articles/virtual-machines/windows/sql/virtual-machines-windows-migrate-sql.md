---
title: SQL Server-adatbázis áttelepítése SQL Serverre a virtuális gép |} A Microsoft Docs
description: Ismerje meg egy helyi felhasználói adatbázis áttelepítése az SQL Server-beli virtuális gépen.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 08/18/2018
ms.author: jroth
ms.openlocfilehash: 298a8597ae111550dfbd44d1e7bd9efb212f08af
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323217"
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>SQL Server-adatbázis áttelepítése Azure-beli virtuális gépen futó SQL Serverre

Nincsenek a helyszíni SQL Server felhasználói adatbázis áttelepítése az SQL Server Azure virtuális gép többféle. Ez a cikk röviden ismertetjük a különböző módszereket, és a legjobb módszer különböző célokra ajánljuk.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Mik azok az elsődleges áttelepítési módszereinek?
Az elsődleges migrálás módszerek a következők:

* A helyszíni biztonsági mentési tömörítéssel és manuálisan másolja a biztonságimásolat-fájlt az Azure virtuális gépen
* URL-cím és a visszaállítás be az Azure virtuális gépen az URL-címet a biztonsági mentés
* Válassza le majd másolja az adathoz és naplófájlhoz az Azure blob storage- és URL-címről az SQL Server Azure virtuális Gépen, majd csatolja
* A helyszíni fizikai gép átalakítása Hyper-V virtuális merevlemez, töltse fel az Azure Blob storage, és telepíteni, a feltöltött új virtuális gépet a virtuális merevlemez
* Szállítási merevlemez Windows Import/Export szolgáltatás használata
* Ha az AlwaysOn rendelkezésre állási csoport központi telepítésének a helyszíni, használja a [Azure replika hozzáadása varázsló](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) replika létrehozásához az Azure és a feladatátvevő, mutasson a felhasználók az Azure database-példány
* Az SQL Server [tranzakciós replikáció](https://msdn.microsoft.com/library/ms151176.aspx) -előfizetőként az Azure SQL Server-példány konfigurálása, és tiltsa le a replikációt, mutasson a felhasználók az Azure database-példány

> [!TIP]
> Ezek a technikák használatával adatbázisok áthelyezése az Azure-beli SQL Server virtuális gépek között. Például nincs támogatott lehetőség a katalógus-lemezkép az SQL Server virtuális gép egy verziójához/kiadásához verzióról a másikra. Ebben az esetben meg kell hozzon létre egy új SQL Server virtuális Gépet az új verziójához/kiadásához, majd a adatáttelepítési eljárásokkal egyik ebben a cikkben az adatbázisok áthelyezéséhez. 

## <a name="choosing-your-migration-method"></a>Az áttelepítési módszer kiválasztása
Az optimális adatátviteli teljesítmény az adatbázisfájlok áttelepíteni az Azure virtuális gép tömörített biztonságimásolat-fájl használatával.

Az adatbázis-áttelepítési folyamat során minimalizálják az állásidőt, használja az AlwaysOn szolgáltatását, vagy a tranzakciós replikáció lehetőséget.

Ha nem lehetséges a fenti módszerek használatát, manuálisan telepítse át az adatbázis. Ezzel a módszerrel, lesz egy adatbázis biztonsági mentése Azure-ba való biztonsági másolatot követ a figyelmeztetésekből kiindulva, és hajtsa végre az adatbázis-visszaállítás. Emellett másolni az adatbázisfájlokat, magukat az Azure-ba, és csatolja őket. Többféleképpen mely szerint végezheti el ehhez a manuális eljáráshoz,-adatbázis áttelepítése az Azure virtuális gépbe.

> [!NOTE]
> Amikor frissíti az SQL Server 2014 vagy SQL Server 2016-ra a SQL Server régebbi verziói, érdemes lehet-e szükség változtatásokra. Azt javasoljuk, hogy, oldja meg a migrálási projekt részeként az új verziót az SQL Server által nem támogatott funkciók az összes függőségét. A támogatott kiadását és forgatókönyvek további információkért lásd: [frissítsen az SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

A következő táblázat felsorolja az egyes elsődleges migrálás módszerek, és ismerteti, amikor az egyes módszerek használata leginkább megfelelő.

| Módszer | Forrás-adatbázis verziója | Cél adatbázis-verzió | Forrás-adatbázis biztonsági másolatának mérete megkötés | Megjegyzések |
| --- | --- | --- | --- | --- |
| [A helyszíni biztonsági mentési tömörítéssel és manuálisan másolja a biztonságimásolat-fájlt az Azure virtuális gépen](#backup-and-restore) |Az SQL Server 2005-ös vagy újabb |Az SQL Server 2005-ös vagy újabb |[Az Azure virtuális gépek tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Ez az egyszerű és tesztelt technika adatbázisok áthelyezése a gépek között. |
| [URL-cím és a visszaállítás be az Azure virtuális gépen az URL-címet a biztonsági mentés](#backup-to-url-and-restore) |Az SQL Server 2012 SP1 CU2 vagy újabb |Az SQL Server 2012 SP1 CU2 vagy újabb |< SQL Server 2016, egyébként < 1 TB-os 12.8 TB | Ez a módszer csak egy másik módja a biztonsági mentési fájl áthelyezéséhez a virtuális géphez az Azure storage használatával. |
| [Válassza le majd másolja az adathoz és naplófájlhoz az Azure blob storage- és mellékeljük az SQL Server Azure virtuális gépen URL-címről](#detach-and-attach-from-url) |Az SQL Server 2005-ös vagy újabb |SQL Server 2014 vagy újabb |[Az Azure virtuális gépek tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Ezt a módszert használja, ha azt tervezi, hogy [az Azure Blob storage szolgáltatás használatával a fájlok tárolási](https://msdn.microsoft.com/library/dn385720.aspx) és csatolja őket egy Azure virtuális gépen, különösen a nagyon nagy méretű adatbázisok futó SQL Server |
| [A helyszíni gépek átalakítása Hyper-V virtuális merevlemezek, töltse fel az Azure Blob storage- és majd a feltöltött virtuális merevlemez használatával új virtuális gép telepítése](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |Az SQL Server 2005-ös vagy újabb |Az SQL Server 2005-ös vagy újabb |[Az Azure virtuális gépek tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Használat [saját SQL Server licence](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md), ha arról, hogy az Ön egy régebbi verzióját az SQL Server-adatbázis áttelepítése, vagy ha a rendszer- és felhasználói adatbázisokat együtt függ-e más adatbázis migrálásának részeként való migrálás a felhasználói adatbázisok és/vagy rendszer-adatbázisokat. |
| [Szállítási merevlemez Windows Import/Export szolgáltatás használata](#ship-hard-drive) |Az SQL Server 2005-ös vagy újabb |Az SQL Server 2005-ös vagy újabb |[Az Azure virtuális gépek tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Használja a [Windows Import/Export szolgáltatás](../../../storage/common/storage-import-export-service.md) manuális másolásának módszere túl lassú, mint például a nagyon nagy méretű adatbázisok esetén |
| [Használja az Azure-replika varázsló hozzáadása](../sqlclassic/virtual-machines-windows-classic-sql-onprem-availability.md) |Az SQL Server 2012 vagy újabb |Az SQL Server 2012 vagy újabb |[Az Azure virtuális gépek tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Állásidőt, akkor használja, ha rendelkezik egy Always On a helyszíni üzembe helyezés |
| [Az SQL Server tranzakciós replikáció használata](https://msdn.microsoft.com/library/ms151176.aspx) |Az SQL Server 2005-ös vagy újabb |Az SQL Server 2005-ös vagy újabb |[Az Azure virtuális gépek tárolási kapacitása](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Akkor használja, ha kell, hogy minimalizálják az állásidőt, és nem tartozik egy Always On a helyszíni üzembe helyezés |

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás
Biztonsági másolatot az adatbázisról a tömörítést a biztonsági másolat a virtuális géphez, és ezután állítsa vissza az adatbázist. Ha a biztonságimásolat-fájl mérete 1 TB-nál nagyobb, kell stripe, mert a Virtuálisgép-lemez maximális mérete 1 TB. Az alábbi általános lépéseket használatával manuális módszerrel a felhasználói adatbázis áttelepítése:

1. Adatbázis teljes biztonsági másolat készítése a helyszíni helyre.
2. Hozzon létre, vagy töltse fel a kívánt SQL Server verzióját egy virtuális gépet.
3. A telepítő kapcsolódási igényei alapján. Lásd: [csatlakozhat egy SQL Server virtuális gépet az Azure-ban (Resource Manager)](virtual-machines-windows-sql-connect.md).
4. A biztonsági mentési fájl(ok) másolása a virtuális géphez a távoli asztal, a Windows Explorer vagy a Másolás parancsot a parancssorba.

## <a name="backup-to-url-and-restore"></a>URL-cím és a visszaállítás történő biztonsági mentés
Biztonsági mentés egy helyi fájlt, helyett használhatja a [URL-címet a biztonsági mentési](https://msdn.microsoft.com/library/dn435916.aspx) és állítsa vissza az URL-címet a virtuális Gépet. Az SQL Server 2016 csíkozott biztonságimásolat-készleteket támogatja, ajánlott a teljesítmény, és meghaladja a méretbeli korlátokat blobonkénti szükséges. A nagyon nagy méretű adatbázisok esetében a használatát a [Windows Import/Export szolgáltatás](../../../storage/common/storage-import-export-service.md) ajánlott.

## <a name="detach-and-attach-from-url"></a>Válassza le és URL-címről csatolása
Válassza le az adatbázis és naplófájlok fájlokat, és ezeket a [Azure Blob storage](https://msdn.microsoft.com/library/dn385720.aspx). Ezután csatolja az adatbázist az Azure virtuális gépen az URL-címből. Akkor használja, ha azt szeretné, hogy a fizikai adatbázis fájlokat tárolni a Blob storage-ban. Ez lehet hasznos, ha nagyon nagy méretű adatbázisokhoz. Az alábbi általános lépéseket használatával manuális módszerrel a felhasználói adatbázis áttelepítése:

1. Válassza le a helyszíni adatbázis-példány származó adatbázisfájlok.
2. A leválasztott adatbázisfájlokat másolja be az Azure blob storage használatával a [AZCopy parancssori segédprogram](../../../storage/common/storage-use-azcopy.md).
3. Az adatbázisfájlok csatolása az Azure URL-címről az SQL Server-példány az Azure-beli virtuális gépen.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Konvertálás virtuális gépre, feltöltés a megadott URL-címre, majd üzembe helyezés új virtuális gépként
Ez a módszer használatával egy helyszíni SQL Server-példányon található összes rendszer és a felhasználó adatbázis migrálása az Azure virtuális géphez. Használja az alábbi általános lépéseket egy teljes SQL Server-példányát, a manuális módszerrel:

1. Fizikai vagy virtuális gépek átalakítása Hyper-V virtuális merevlemezeket.
2. VHD-fájlok feltöltése az Azure Storage használatával a [Add-AzureVHD parancsmag](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Egy új virtuális gép üzembe a feltöltött virtuális merevlemez használatával.

> [!NOTE]
> Teljes alkalmazást telepítene át, érdemes lehet [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Szállítási merevlemez-meghajtó
Használja a [Windows Import/Export szolgáltatás metódus](../../../storage/common/storage-import-export-service.md) olyan helyzetekben, ahol a hálózaton keresztül feltöltése elfogadhatatlanul költséges vagy nem megvalósítható az Azure Blob storage nagy mennyiségű Fájladat át. Egy vagy több merevlemezeket valamely Azure adatközpontnak, ahol az adatokat a tárfiók fel lesz töltve az adatokat tartalmazó küldeni ennek a szolgáltatásnak.

## <a name="next-steps"></a>További lépések
Azure virtuális gépeken futó SQL Server rendszerrel kapcsolatos további információkért lásd: [SQL Server on Azure Virtual Machines – áttekintés](virtual-machines-windows-sql-server-iaas-overview.md).

> [!TIP]
> Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

Egy rögzített lemezképből egy Azure SQL Server virtuális gép létrehozásával kapcsolatos útmutatóért lásd: [tippek és trükkök a "klónozást" Azure SQL virtuális gépek a rögzített lemezképeket](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) a CSS SQL Server Engineers blogon lévő.

