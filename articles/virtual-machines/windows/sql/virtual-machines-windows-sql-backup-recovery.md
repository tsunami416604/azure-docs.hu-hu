---
title: Biztonsági mentése és visszaállítása az SQL Server Azure virtuális gépeken |} Microsoft Docs
description: Az Azure virtuális gépeken futó SQL Server-adatbázisok biztonsági mentése és visszaállítása szempontokat ismerteti.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: 29abd62b0602686c912f022ec8870dcb6f1b39eb
ms.sourcegitcommit: 0a7c9629dea6f5eb52cc45a1e31112def210dfc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34730883"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Biztonsági mentés és visszaállítás Azure-beli SQL Server-alapú virtuális gépeken

Ez a cikk nyújt útmutatást a biztonsági mentési és visszaállítási lehetőségekről az SQL Server fut, a Windows Azure virtuális gépeken. Az Azure Storage minden Azure virtuális lemez adatvesztés vagy fizikai adatsérülés elleni védelem biztosításához három másolatot tart fenn. Ebből kifolyólag a helyszínen, eltérően nem kell összpontosítani a hardver meghibásodása. Azonban az SQL Server-adatbázisok alkalmazás vagy felhasználó hibákat, például az adatok véletlen beszúrások és a törlések elleni védelem érdekében továbbra is biztonsági. Ebben a helyzetben fontos állíthatja helyre az adott időben.

Ez a cikk első része a rendelkezésre álló biztonsági mentési és visszaállítási lehetőségek áttekintést nyújt. A következő szakaszok nyújtanak további információt az egyes stratégia követi.

## <a name="backup-and-restore-options"></a>Biztonsági mentési és helyreállítási lehetőségek

Az alábbi táblázatban bővebb információt a különböző biztonsági mentési és visszaállítási lehetőségek az SQL Server Azure virtuális gépeken futó:

| Stratégia | SQL-verziók | Leírás |
|---|---|---|---|
| [Automatikus biztonsági mentés](#automated) | 2014<br/> 2016<br/> 2017 | Automatikus biztonsági mentés lehetővé teszi a biztonsági másolatok rendszeres az összes olyan adatbázis egy SQL Server virtuális gépen. Biztonsági mentés akár 30 napig tárolja az Azure storage. SQL Server 2016-kezdve automatikus biztonsági mentés v2 például kézi ütemezés és a teljes gyakoriságát és a naplóalapú biztonsági mentések konfigurálása további lehetőséget kínál. |
| [Azure biztonsági mentés SQL virtuális gépek](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Azure biztonsági mentés egy vállalati osztály biztonsági mentési lehetőséget nyújt az SQL Server rendszert futtató Azure virtuális gépeken. Ezzel a szolgáltatással több kiszolgáló, és akár több ezer adatbázis biztonsági másolatai központilag kezelheti. Adatbázisok visszaállítása végezhető el egy adott pontra alkalommal járnak a portálon. Testre szabható adatmegőrzési fenntarthatja a biztonsági mentések évig kínál. Ez a funkció jelenleg nyilvános előzetes verziójához. |
| [Manuális biztonsági mentés](#manual) | Összes | Az SQL Server verziójától függően többféleképpen manuálisan biztonsági mentése és visszaállítása egy Azure virtuális gépen futó SQL Server. Ebben a forgatókönyvben telepítésért felelős, hogyan az adatbázisok biztonsági mentése és a tárolási helyét és a biztonsági mentése. |

A következő szakaszok ismertetik részletesebben lehetőségekről. Ez a cikk utolsó része egy szolgáltatás mátrix formájában összegzését tartalmazza.

## <a id="autoamted"></a> Automatikus biztonsági mentés

Automatikus biztonsági mentés a Windows Azure virtuális gépen futó SQL Server Standard és Enterprise kiadás automatikus biztonsági mentési szolgáltatást biztosít. Ez a szolgáltatás által biztosított a [SQL Server infrastruktúra-szolgáltatási ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md), amely automatikusan települ az SQL Server Windows virtuális gép képfájljait az Azure portálon.

Összes adatbázis konfigurált Azure storage-fiók biztonsági mentése. Biztonsági mentések titkosítva, és akár 30 napig őrzi meg.

SQL Server 2016 és a nagyobb virtuális gépek kínál további testreszabási beállításai az automatikus biztonsági mentés 2. Ezek a fejlesztések közé tartoznak:

- Rendszer-adatbázis biztonsági mentése
- Manuális biztonsági mentés ütemezését és időkerete
- Teljes telepítési és a napló biztonsági mentési gyakoriság fájl

Adatbázis visszaállítása, keresse meg a szükséges biztonsági mentési (oka) t a tárfiókban lévő, és az SQL virtuális gép az SQL Server Management Studio (SSMS) vagy a Transact-SQL-parancsokat a visszaállításhoz.

SQL virtuális gépek automatikus biztonsági mentés konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkeket:

- **SQL Server 2016/2017**: [automatikus biztonsági mentési v2 az Azure virtuális gépeken ](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [automatikus biztonsági mentés SQL Server 2014 virtuális gépekhez](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Azure biztonsági mentés SQL virtuális gépek

[Azure biztonsági mentés](/azure/backup/) egy vállalati osztály biztonsági mentési lehetőséget nyújt az Azure virtuális gépeken futó SQL Server. Minden biztonsági mentés tárolja és kezeli a Recovery Services-tároló. Van több előnye, hogy a megoldás biztosít, különösen a vállalatok számára:

- **Nulla-infrastruktúra biztonsági mentés**: nincs tartalék kiszolgáló vagy a tárolóhelyek kezeléséhez.
- **Skála**: sok SQL virtuális gép, és akár több ezer adatbázis védelmét.
- **Használatalapú fizetés**: Ezzel a funkcióval egy külön Azure Backup szolgáltatás által biztosított szolgáltatás, de, az összes Azure-szolgáltatásokkal, csak kell fizetnie a valóban használt funkciókért.
- **Központi kezelés és monitorozás**: központilag kezelheti a biztonsági másolatok, többek között a támogató Azure biztonsági mentés, egyetlen Irányítópulton az Azure-ban a többi munkaterhelését.
- **A házirend-alapú biztonsági mentési és adatmegőrzési**: hozzon létre szabványos biztonsági mentési házirendek rendszeres biztonsági mentéséhez. Biztonsági mentések fenntartásához évig adatmegőrzési létrehozásához.
- **SQL Always On támogatása**: észlelése és egy SQL Server Always On konfigurációs védelméhez és fogadják el a biztonsági mentési rendelkezésre állási csoport biztonsági mentési beállítások.
- **15 perces helyreállítási-célkitűzés (RPO)**: konfigurálása SQL tranzakciós naplók biztonsági másolatainak legfeljebb 15 percenként.
- **Időponthoz kötött visszaállításra**: helyreállíthatják az adatbázisokat az adott időben ne kelljen manuálisan állítsa vissza a több teljes, különbözeti, a portál használatával, valamint naplófájl-biztonsági mentések.
- **E-mailes riasztásokhoz hibák konszolidált**: konfigurálása konszolidált értesítő e-mailek az esetleges hibákat.
- **Szerepköralapú hozzáférés-vezérlés**: határozza meg, akik kezelheti a biztonsági mentési és visszaállítási műveleteket a portálon keresztül.

A gyors megtudhatja, hogyan működik együtt a bemutató tekintse meg a következő videót:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2dNbw]

Az Azure biztonsági mentési megoldás SQL virtuális gépek jelenleg nyilvános előzetes verziójához. További információkért lásd: [Azure SQL Server-adatbázis biztonsági mentése](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Manuális biztonsági mentés

Ha szeretné manuálisan kezelheti a biztonsági mentési és visszaállítási műveletek az SQL virtuális gépeken, többféle módon használja az SQL Server verziójától függően. Biztonsági mentés és helyreállítás áttekintése tekintse meg a következő cikkekben talál az SQL Server verziójától függően egyikét:

- [Biztonsági mentés és visszaállítás az SQL Server 2016-os és újabb verzióihoz](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Biztonsági mentés és visszaállítás szolgáltatás SQL Server 2014 rendszerhez](https://msdn.microsoft.com/en-us/library/ms187048%28v=sql.120%29.aspx)
- [Biztonsági mentés és visszaállítás az SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Biztonsági mentése és visszaállítása az SQL Server SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Biztonsági mentés és visszaállítás az SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

Az alábbi szakaszok ismertetik több manuális biztonsági mentés, és visszaállítási lehetőségek részletesebben.

### <a name="backup-to-attached-disks"></a>Csatlakoztatott lemezek történő biztonsági mentés

Az Azure virtuális gépeken futó SQL Server, a natív biztonsági mentéssel, és állítsa vissza a biztonsági mentési fájlok és a virtuális gép csatlakoztatott lemezek használt módszerek. Van azonban a megadott korlát a hozzácsatolhat egy Azure virtuális gépet, amely alapján a lemezek számát a [a virtuális gép mérete](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). A terhelését, fontolja meg a Lemezkezelés is van.

Kézi létrehozása az SQL Server Management Studio (SSMS) vagy a Transact-SQL használatával teljes adatbázis biztonsági másolatának példáért lásd: [hozzon létre egy teljes adatbázis biztonsági mentését](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Biztonsági mentés URL-címe

SQL Server 2012 SP1 CU2 kezdve biztonsági mentése és visszaállítása közvetlenül a Microsoft Azure Blob storage, is nevezik biztonsági mentés URL-címre. SQL Server 2016 rendszerben jelent meg a szolgáltatás a következő fejlesztéseket is:

| 2016 továbbfejlesztése | Részletek |
| --- | --- |
| **Csíkozást** |A Microsoft Azure blob Storage tárolóban való biztonsági mentéshez, SQL Server 2016 támogatja a biztonsági mentés engedélyezése a nagy adatbázisok 12.8 TB legfeljebb biztonsági másolatának több blobot. |
| **Pillanatkép biztonsági másolatából** |Azure pillanatképek révén a SQL Server-fájl-pillanatkép biztonsági másolatából szinte azonnali biztonsági mentéseit és az adatbázis-fájlok tárolása az Azure Blob storage szolgáltatással gyors helyreállítást biztosít. Ez a funkció lehetővé teszi egyszerűsítése érdekében a biztonsági mentés és visszaállítás házirendek. Fájl-pillanatkép biztonsági másolatából a visszaállítás egy korábbi időpontra pont is támogatja. További információkért lásd: [pillanatképes biztonsági adatbázis-fájlok az Azure-ban](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

További információkért lásd: a következő cikkekben talál az SQL Server verziójától függően közül:

- **SQL Server 2016/2017**: [SQL Server biztonsági másolat segítségével URL-címe](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 biztonsági másolat segítségével URL-címe](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012-ben biztonsági mentés URL-címe](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Felügyelt biztonsági mentés

Az SQL Server 2014-kezdve való felügyelt biztonsági mentésének automatizálja a az Azure storage biztonsági mentések létrehozását. A háttérben való felügyelt biztonsági mentésének alkalmazza, ez a cikk az előző szakaszban leírt URL-cím szolgáltatás biztonsági másolat. Felügyelt biztonsági mentés is az alapul szolgáló szolgáltatás, amely támogatja az SQL Server-a virtuális gép automatikus biztonsági mentés szolgáltatást.

Felügyelt biztonsági mentés készült SQL Server 2016-os verziótól kezdve további beállítások biztonsági mentési és a teljes rendszer-adatbázis és a napló biztonsági mentési gyakoriság ütemezése.

További információkért tekintse meg a következő cikkekben talál az SQL Server verziójától függően egyikét:

- [Kezeli az SQL Server 2016 Microsoft Azure biztonsági mentési és újabb verziók](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Felügyelt biztonsági mentés az SQL Server 2014 Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Döntés a mátrix

Az alábbi táblázat foglalja össze az egyes biztonsági mentési és visszaállítási lehetőségek az SQL Server Azure virtuális gép képességeit.

|| **Automatikus biztonsági mentés** | **Azure biztonsági mentés SQL virtuális gépek** | **Manuális biztonsági mentés** |
|---|---|---|---|
| További Azure-szolgáltatás szükséges |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| A biztonsági mentési házirend konfigurálása az Azure-portálon | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure-portálon adatbázisainak visszaállítása |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Egy irányítópulton több kiszolgáló kezelése |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Adott időpontnak megfelelő helyreállítás | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15 perces helyreállítási időkorlát (RPO) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Rövid távú biztonsági mentés megőrzési házirend (nap) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Hosszú távú biztonsági mentés megőrzési házirend (hónap, év) |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| SQL Server Always On beépített támogatása |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konszolidált e-mailes riasztásokhoz hibák |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Szerepköralapú hozzáférés-vezérlés |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Recovery Services-tároló történő biztonsági mentés |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure-tárfiókok történő biztonsági mentés | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Csatlakoztatott lemezek, a virtuális Gépre történő biztonsági mentés |   |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Az SSMS vagy a Transact-SQL-parancsfájlok adatbázisainak visszaállítása | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>További lépések

Ha azt tervezi, az adott SQL Server egy Azure virtuális gép, ez az útmutató az üzembe helyezési útmutató található: [hogyan egy Windows SQL Server rendszerű virtuális gép az Azure portálon](virtual-machines-windows-portal-sql-server-provision.md).

Bár a biztonsági mentés és visszaállítás segítségével az adatok áttelepítését, vannak a potenciálisan könnyebb adatok áttelepítési útvonalak az SQL Server egy Azure virtuális gépen. Beállítások és javaslatok ismertetését, lásd: [adatbázis Migrálása az SQL Server egy Azure virtuális gépen](virtual-machines-windows-migrate-sql.md).