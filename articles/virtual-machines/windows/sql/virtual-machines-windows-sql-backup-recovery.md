---
title: "Biztonsági mentése és visszaállítása az SQL Server |} Microsoft Docs"
description: "Az Azure virtuális gépeken futó SQL Server-adatbázisok biztonsági mentése és visszaállítása szempontokat ismerteti."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: mikeray
ms.openlocfilehash: 65557938673c5442758396a47873be1016e0f71b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Biztonsági mentés és visszaállítás Azure-beli SQL Server-alapú virtuális gépeken
## <a name="overview"></a>Áttekintés
Az Azure Storage minden Azure virtuális lemez adatvesztés vagy fizikai adatsérülés elleni védelem biztosításához 3 másolatot tart fenn. Ebből kifolyólag eltérően a helyszínen, nem kell aggódnia ezeket. Azonban érdemes továbbra is biztonsági másolatot készíteni, alkalmazás vagy felhasználó hibákat (pl. helytelen adatokat beszúrni vagy tábla törlésekor) elleni védelem érdekében az SQL Server-adatbázisok és visszaállítása egy alkalommal.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Az Azure virtuális gépeken futó SQL Server, a natív biztonsági mentéssel, és állítsa vissza a csatlakoztatott lemezek használatát a biztonságimásolat-fájlokat és technikákat. Van azonban a megadott korlát a hozzácsatolhat egy Azure virtuális gépet, amely alapján a lemezek számát a [a virtuális gép mérete](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A terhelését, fontolja meg a Lemezkezelés is van.

Az SQL Server 2014-kezdve biztonsági mentése és visszaállítása a Microsoft Azure Blob storage. SQL Server 2016-fejlesztéseket jeleníti meg ezt a lehetőséget is biztosít. Az adatbázis a Microsoft Azure Blob storage-ban tárolt fájlok, SQL Server 2016 emellett beállítást szinte azonnali biztonsági mentések és a gyors visszaállítások Azure pillanatképek. Ez a cikk áttekintést ezeket a beállításokat, és további információk találhatók [SQL Server biztonsági másolat és helyreállítás a Microsoft Azure Blob Storage szolgáltatás](https://msdn.microsoft.com/library/jj919148.aspx).

> [!NOTE]
> A nagyon nagy adatbázisok biztonsági mentésével a lehetőségek tárgyalását lásd: [több terabájtos SQL Server adatbázis biztonsági stratégiák az Azure virtuális gépek](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).
> 
> 

Az alábbiakban egy Azure virtuális gépen támogatott SQL Server különböző verzióihoz való információkat tartalmazza.

## <a name="sql-server-virtual-machines"></a>SQL Server virtuális gépek
Ha az SQL Server-példány egy Azure virtuális gép fut, az adatbázisfájlok már található adatlemezek az Azure-ban. Ezek a lemezek helyezkednek el az Azure Blob Storage tárolóban. Ezért a biztonsági másolat készítése az adatbázis és a módszerekkel, okait tegye módosítás némileg. Vegye figyelembe a következőket. 

* Már nincs szüksége az adatbázisról biztonsági mentést védelmet biztosít a hardver-vagy arra, mert a Microsoft Azure a Microsoft Azure szolgáltatás részeként ez védelmet biztosít.
* Továbbra is szeretné az adatbázisról biztonsági mentést felhasználói hibák ellen, vagy archiválási célokból, a szabályozási okok vagy a felügyeleti célú védelem biztosításához.
* A biztonságimásolat-fájl közvetlenül az Azure-ban tárolhatók. További információkért tekintse meg a következő szakaszok az SQL Server különböző verzióit útmutatást nyújtó.

## <a name="sql-server-2016"></a>SQL Server 2016
Támogatja a Microsoft SQL Server 2016 [biztonsági mentése és visszaállítása a Azure BLOB](https://msdn.microsoft.com/library/jj919148.aspx) szolgáltatások található az SQL Server 2014. De a következő fejlesztéseket tartalmazza:

| 2016 továbbfejlesztése | Részletek |
| --- | --- |
| **Csíkozást** |A Microsoft Azure blob Storage tárolóban való biztonsági mentéshez, SQL Server 2016 támogatja a biztonsági mentés engedélyezése a nagy adatbázisok 12.8 TB legfeljebb biztonsági másolatának több blobot. |
| **Pillanatkép biztonsági másolatából** |Azure pillanatképek révén a SQL Server-fájl-pillanatkép biztonsági másolatából szinte azonnali biztonsági mentéseit és az adatbázis-fájlok tárolása az Azure Blob storage szolgáltatással gyors helyreállítást biztosít. Ez a funkció lehetővé teszi egyszerűsítése érdekében a biztonsági mentés és visszaállítás házirendek. Fájl-pillanatkép biztonsági másolatából a visszaállítás egy korábbi időpontra pont is támogatja. További információkért lásd: [pillanatképes biztonsági adatbázis-fájlok az Azure-ban](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx). |
| **Kezeli a biztonsági mentési ütemezés** |SQL Server felügyelt biztonsági mentésének Azure mostantól támogatja az egyéni ütemezések. További információkért lásd: [SQL Server felügyelt Microsoft Azure Backup](https://msdn.microsoft.com/library/dn449496.aspx). |

Az SQL Server 2016 Azure Blob storage használata esetén a képességeit oktatóanyagok esetén lásd: [oktatóanyag: a Microsoft Azure Blob storage szolgáltatást használó adatbázisok SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## <a name="sql-server-2014"></a>SQL Server 2014
Az SQL Server 2014 a következő fejlesztéseket tartalmazza:

1. **Biztonsági mentése és visszaállítása az Azure-bA**:
   
   * *SQL Server biztonsági másolat URL-címre* most már támogatja az SQL Server Management Studio. A beállítás a biztonsági másolatot készítsen az Azure biztonsági mentési vagy helyreállítási feladat, vagy a karbantartási terv varázsló használata az SQL Server Management Studio most érhető el. További információkért lásd: [SQL Server biztonsági másolat URL-címre](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
   * *SQL Server felügyelt biztonsági mentés az Azure-bA* , amely lehetővé teszi az automatikus biztonsági mentés új funkciókkal rendelkezik. Ez különösen fontos a egy Azure gépen futó SQL Server 2014-példányok biztonsági felügyeletének automatizálására. További információkért lásd: [SQL Server felügyelt Microsoft Azure Backup](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
   * *Automatikus biztonsági mentés* biztosít ahhoz, hogy automatikusan további automation *SQL Server felügyelt biztonsági mentésének Azure* összes meglévő és új adatbázishoz az SQL Server virtuális gép az Azure-ban. További információk: [Automated Backup for SQL Server in Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md) (Az SQL Server automatikus biztonsági mentése Azure virtuális gépeken).
   * SQL Server 2014-kiszolgálók regisztrálása összes lehetséges áttekintését lásd: [SQL Server biztonsági másolat és helyreállítás a Microsoft Azure Blob Storage szolgáltatás](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
2. **Titkosítási**: SQL Server 2014 támogatja a titkosított adatok, a biztonsági másolat létrehozásakor. Támogatja a több titkosítási algoritmusok és használatának osf egy tanúsítvánnyal vagy aszimmetrikus kulcs. További információkért lásd: [biztonsági másolatok titkosításának](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012-ben
Az SQL Server biztonsági mentése és visszaállítása az SQL Server 2012 részletes információkért lásd: [biztonsági mentése és visszaállítása az SQL Server-adatbázisok (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

Az SQL Server 2012 SP1 kumulatív frissítés 2-től kezdődően biztonsági másolat, és állítsa vissza az Azure Blob Storage szolgáltatásból. Ez a fejlesztés használható adatbázisok biztonsági mentése SQL Server az Azure virtuális gép vagy egy helyszíni példányát futó SQL Server-kiszolgálón. További információkért lásd: [SQL Server biztonsági mentése és visszaállítása az Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Az Azure Blob storage szolgáltatással előnyei közé kikerülheti a csatlakoztatott lemezek, a könnyű kezelhetőséget, a biztonságimásolat-fájlt egy Azure virtuális gépen futó SQL Server-példány egy másik példánya közvetlen rendelkezésre állását a 16 lemez korlátot , vagy egy helyszíni példányát az áttelepítési vagy katasztrófa utáni helyreállítás céljából. Egy Azure blob storage szolgáltatás az SQL Server biztonsági mentések előnyeinek teljes listáját lásd: a *előnyöket* szakasz [SQL Server biztonsági mentése és visszaállítása az Azure Blob Storage Service](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Ajánlott eljárás javaslatok és hibaelhárítási információkat lásd: [biztonsági mentés és visszaállítás az ajánlott eljárások (Azure Blob Storage szolgáltatás)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## <a name="sql-server-2008"></a>SQL Server 2008
SQL Server biztonsági másolat és helyreállítás az SQL Server 2008 R2: [biztonsági mentése és az adatbázisok visszaállítása az SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

SQL Server biztonsági mentése és visszaállítása az SQL Server 2008, lásd: [biztonsági mentése és az adatbázisok visszaállítása az SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Következő lépések
Ha azt tervezi, az SQL Server egy Azure virtuális gépen a telepítés, üzembe helyezési útmutató található a következő oktatóanyag: [az Azure Resource Manager Azure SQL Server virtuális gépek kiépítése](virtual-machines-windows-portal-sql-server-provision.md).

Bár a biztonsági mentés és visszaállítás segítségével az adatok áttelepítését, vannak a potenciálisan könnyebb adatok áttelepítési útvonalak az SQL Server egy Azure virtuális gépen. Beállítások és javaslatok ismertetését, lásd: [adatbázis Migrálása az SQL Server egy Azure virtuális gépen](virtual-machines-windows-migrate-sql.md).

Tekintse át az egyéb [SQL Server Azure virtuális gépek futtatásához szükséges erőforrások](virtual-machines-windows-sql-server-iaas-overview.md).

