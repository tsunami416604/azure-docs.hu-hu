---
title: Biztonsági mentés és visszaállítás Azure virtuális gépeken futó SQL Serverhez |} A Microsoft Docs
description: Az Azure Virtual machines szolgáltatásban futó SQL Server-adatbázisok biztonsági mentésével és visszaállításával kapcsolatos szempontokat ismerteti.
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
ms.openlocfilehash: ec147355d6f61a282a0e6b0950fe03ba0e2341f5
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530374"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Biztonsági mentés és visszaállítás Azure-beli SQL Server-alapú virtuális gépeken

Ez a cikk útmutatást nyújt a biztonsági mentési és visszaállítási lehetőségekről az SQL Server az Azure-beli Windows virtuális gépen futó. Az Azure tárolás három másolatot tart fenn minden Azure Virtuálisgép-lemez az adatvesztést vagy fizikai adatsérülés elleni védelmének biztosításához. Ebből kifolyólag ellentétben a helyszínen, nem kell összpontosíthat a hardveres hibák esetén. Azonban az SQL Server adatbázisok alkalmazás- vagy hibák, például az adatok véletlen beszúrások és a törlések ellen védelmet biztosító továbbra is biztonsági. Ebben az esetben fontos tudni adott időpontra való visszaállítása.

Ez a cikk első részében a rendelkezésre álló biztonsági mentési és visszaállítási lehetőségek áttekintést nyújt. A szakaszok további információval szolgálnak az egyes stratégiák követ.

## <a name="backup-and-restore-options"></a>Biztonsági mentési és visszaállítási lehetőségek

A következő táblázat információkat a különböző biztonsági mentési és helyreállítási lehetőségek, az SQL Server Azure virtuális gépeken futó:

| Stratégia | SQL-verziók | Leírás |
|---|---|---|---|
| [Automatikus biztonsági mentés](#automated) | 2014<br/> 2016<br/> 2017 | Automatikus biztonsági mentés lehetővé teszi, hogy az összes adatbázis egy SQL Server virtuális gépen rendszeres biztonsági mentések ütemezését. Biztonsági másolatok az Azure storage akár 30 napig tárolja. Automatikus biztonsági mentés v2 kínál SQL Server 2016-os verziótól kezdve további beállítások, például manuális ütemezés, és a teljes gyakoriságát és naplóalapú biztonsági mentések konfigurálása. |
| [Az Azure Backup az SQL virtuális gépek](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Az Azure Backup egy vállalati osztály biztonsági mentési lehetőséget nyújt az SQL Server rendszert futtató Azure virtuális gépeken. Ezt a szolgáltatást, a biztonsági másolatok több kiszolgálók és adatbázisok ezreit központilag kezelheti. Adatbázisok időben a portálon egy adott időpontra állíthatók vissza. Biztosít egy testre szabható adatmegőrzési szabály fenntarthatja a biztonsági mentések évig. Ez a funkció jelenleg nyilvános előzetes verzióban érhető el. |
| [Manuális biztonsági mentés](#manual) | Összes | Az SQL Server verziójától függően többféleképpen manuálisan biztonsági mentése és visszaállítása egy Azure-beli virtuális gépen futó SQL Server. Ebben a forgatókönyvben Ön felelős hogyan az adatbázisok biztonsági mentése, és a tárolási helyét és kezelésére, ezeket a biztonsági másolatokat. |

A következő szakaszok ismertetik az egyes lehetőségek részletesebben. Ez a cikk utolsó szakasza összefoglalja funkció mátrix formájában.

## <a id="automated"></a> Automatikus biztonsági mentés

Automatikus biztonsági mentés egy Azure-beli Windows virtuális gépen futó SQL Server Standard és Enterprise kiadások automatikus biztonsági mentési szolgáltatást biztosít. Ez a szolgáltatás által biztosított a [SQL Server IaaS-ügynök bővítmény](virtual-machines-windows-sql-server-agent-extension.md), amely automatikusan települ az SQL Server Windows virtuálisgép-lemezképek az Azure Portalon.

Minden adatbázis biztonsági mentése konfigurálása az Azure storage-fiókhoz. Biztonsági másolatok is titkosítva, és akár 30 napig őrizzük meg.

Az SQL Server 2016 és újabb virtuális gépek kínálnak az automatikus biztonsági mentés v2 további testreszabási lehetőségek. Ezek a fejlesztések a következők:

- Rendszer-adatbázisok biztonsági mentése
- Manuális biztonsági mentés ütemezése és időtartomány
- Teljes és a log fájl biztonsági mentés gyakorisága

Adatbázis visszaállítása, keresse meg a szükséges biztonsági mentési fájlokat a storage-fiókban, és hajtson végre visszaállítást az SQL virtuális gép az SQL Server Management Studio (SSMS) vagy a Transact-SQL-parancsok használatával.

További információ az SQL virtuális gépek automatikus biztonsági mentés konfigurálásához tekintse meg a következő cikkeket:

- **SQL Server 2016/2017**: [Azure-beli virtuális gépek automatikus biztonsági mentés v2](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Automatikus biztonsági mentés az SQL Server 2014 virtuális gépek](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Az Azure Backup az SQL virtuális gépek (nyilvános előzetes verzió)

[Az Azure Backup](/azure/backup/) egy vállalati osztály biztonsági mentési lehetőséget nyújt az Azure-beli virtuális gépeken futó SQL Serverhez. Minden biztonsági mentés tárolja és kezeli a Recovery Services-tárolóba. Van több előnye is van, amely ezt a megoldást biztosít, különösen a nagyvállalatok számára:

- **Nulla-infrastruktúra biztonsági mentését**: Nem kell tartalék kiszolgáló vagy a tárolóhelyek kezeléséhez.
- **Méretezési csoport**: Számos SQL virtuális gépek és adatbázisok ezreit védelmét.
- **Pay-As-You-Go**: Ez a funkció önálló szolgáltatásként az Azure Backup által biztosított, de, az összes Azure-szolgáltatásokkal, csak fizetnie kell fizetni.
- **Központi felügyeleti és monitorozási**: Központilag kezelhető az összes, a biztonsági mentések, beleértve a más számítási feladatok, amely az Azure Backup támogatja az Azure-ban egyetlen irányítópultról.
- **Készített biztonsági mentési és adatmegőrzési szabályzat**: Hozzon létre standard biztonsági mentési házirendek rendszeres biztonsági. Évig fenntartása a biztonsági mentések megőrzési házirendeket hoz létre.
- **Támogatja az SQL Always**: Észlelése és a egy SQL Server Always On konfigurációs védelme, és figyelembe veszi a biztonsági mentési rendelkezésre állási csoport biztonsági mentési preferenciáját.
- **15 perces Helyreállításipont-célkitűzés (RPO)**: Konfigurálja az legfeljebb 15 percenként SQL tranzakciós naplók biztonsági mentését.
- **Időponthoz kötött visszaállítás pont**: A portál használatával egy adott időpontra adatbázisok helyreállításához időben manuálisan állítsa vissza a teljes, különbségi több nélkül, és biztonsági másolataihoz.
- **E-mailes riasztásokhoz hibák összesített**: Az esetleges hibák összevont e-mail-értesítések beállítása.
- **Szerepköralapú hozzáférés-vezérlés**: Határozza meg, aki kezelheti biztonsági mentési és visszaállítási műveleteket a portálon keresztül.

Röviden, hogyan működik együtt egy bemutatót tekintse meg a következő videót:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2dNbw]

Az Azure Backup megoldás az SQL virtuális gépek jelenleg nyilvános előzetes verzióban érhető el. További információkért lásd: [az Azure SQL Server-adatbázis biztonsági mentése](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Manuális biztonsági mentés

Ha azt szeretné, manuális biztonsági mentés kezelése és visszaállítási műveleteket az SQL virtuális gépeken, többféle módon használja az SQL Server verziójától függően. Biztonsági mentés és helyreállítás áttekintése tekintse meg az SQL Server verziójától függően a következő cikkeket:

- [Biztonsági mentés és visszaállítás az SQL Server 2016 és újabb verziók](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Biztonsági mentés és visszaállítás az SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Biztonsági mentés és visszaállítás az SQL Server 2012 termékhez](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Biztonsági mentés és visszaállítás az SQL Server az SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Biztonsági mentés és visszaállítás az SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

A következő szakaszok ismertetik a különböző manuális biztonsági mentés, és visszaállítási lehetőségek részletesebben.

### <a name="backup-to-attached-disks"></a>A csatlakoztatott lemezek biztonsági mentése

Azure-beli virtuális gépeken futó SQL Server natív biztonsági mentését, és állítsa vissza a csatlakoztatott lemezek használatával a virtuális gép biztonsági mentési fájlt a cél technikákat. Az Azure virtuális gép, amely alapján csatlakoztathat lemezeket száma korlátozva van azonban a [a virtuális gép méretét](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Nincs fontolja meg a Lemezkezelés járó többletterhelést.

Kézi létrehozása az SQL Server Management Studio (SSMS) vagy a Transact-SQL használata teljes adatbázis biztonsági másolatát egy példa: [hozzon létre egy teljes adatbázis biztonsági mentési](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Biztonsági mentés URL-címe

Az SQL Server 2012 SP1 CU2 verziótól kezdve biztonsági mentése és visszaállítása közvetlenül a Microsoft Azure Blob storage, így is biztonsági mentés URL-címre. Az SQL Server 2016 is bevezetett a funkció az alábbi fejlesztéseket tartalmazzák:

| 2016-fejlesztés | Részletek |
| --- | --- |
| **Csíkozást** |Ha biztonsági másolatot a Microsoft Azure blob storage, SQL Server 2016 támogatja a biztonsági mentés engedélyezése a nagy méretű adatbázisokhoz, 12.8 TB legfeljebb mentésére több blobok. |
| **Pillanatkép biztonsági másolatából** |Azure pillanatképek révén a Backup az SQL Server biztonságimásolat-pillanatkép készítése szinte azonnali biztonsági mentéseket és az adatbázisfájlokat tárolja a rendszer az Azure Blob storage szolgáltatás gyors helyreállítást biztosít. Ez a funkció lehetővé teszi, hogy egyszerűsítse a biztonsági mentés, és állítsa vissza a szabályzatok. Fájlpillanatkép-biztonsági mentés is támogatja a pont, időponthoz kötött visszaállítás. További információkért lásd: [pillanatkép készítése Adatbázisfájlokról az Azure-ban](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

További információkért tekintse meg az egyik az SQL Server verziójától függően a következő cikkeket:

- **SQL Server 2016/2017**: [Az SQL Server biztonsági másolat az URL-címe](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [Az SQL Server 2014 biztonsági mentés URL-címe](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [Az SQL Server 2012-ben biztonsági mentés URL-címe](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Managed Backup

Az SQL Server 2014 kezdve felügyelt biztonsági mentési automatizálja az Azure storage-biztonsági mentések létrehozását. A színfalak mögött felügyelt biztonsági mentésének lehetővé teszi a biztonsági mentés, ez a cikk korábbi szakaszában leírt URL-cím funkció használata. A felügyelt biztonsági mentés is az alapul szolgáló szolgáltatás, amely támogatja az SQL Server virtuális gép automatikus biztonsági mentés szolgáltatás.

Az SQL Server 2016-es verziótól kezdve a felügyelt biztonsági mentés van további beállításokat a feladatütemezést, a biztonsági mentési és a teljes rendszer-adatbázisok és a naplóalapú biztonsági mentés gyakorisága.

További információkért tekintse meg az SQL Server verziójától függően a következő cikkeket:

- [Managed Backup a Microsoft Azure SQL Server 2016 és újabb verziók](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Managed Backup a Microsoft Azure az SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Döntési mátrix

A következő táblázat összefoglalja a funkciókat az egyes biztonsági mentési és helyreállítási lehetőségek, az SQL Server virtuális gépek az Azure-ban.

|| **Automatikus biztonsági mentés** | **Az Azure Backup az SQL** | **Manuális biztonsági mentés** |
|---|---|---|---|
| További Azure-szolgáltatás szükséges |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| A biztonsági mentési szabályzat konfigurálása az Azure Portalon | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Az Azure portal adatbázisok visszaállítása |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Egyetlen irányítópulton több kiszolgáló kezelése |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Adott időpontnak megfelelő helyreállítás | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15 perces Helyreállításipont-célkitűzés (RPO) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Rövid távú biztonsági mentés megőrzési házirend (nap) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Hosszú távú adatmegőrzési házirend (hónap, év) |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| SQL Server Always On beépített támogatása |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Biztonsági másolat az Azure Storage-fiókok | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatikus) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatikus) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(az ügyfél által felügyelt) |
| A tárolási és biztonsági mentési fájlok kezelése | | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Csatlakoztatott lemezek a virtuális gépen történő biztonsági mentés |   |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Központi testre szabható biztonsági másolati jelentések |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Konszolidált e-mailes riasztásokhoz hibák |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Az Azure Monitor naplóira alapján figyelésének testreszabása |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Az ssms-t vagy a Transact-SQL-parancsfájlok biztonsági mentési feladatok figyelése | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Az ssms-t vagy a Transact-SQL szkriptek adatbázisok visszaállítása | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>További lépések

Ha tervezi az SQL Server egy Azure-beli virtuális gépen a telepítés, üzembe helyezési útmutató az alábbi útmutatóban találja: [Az Azure Portalon Windows SQL Server virtuális gép kiépítése](virtual-machines-windows-portal-sql-server-provision.md).

Biztonsági mentés és visszaállítás segítségével az adatok áttelepítését, bár vannak a potenciálisan könnyebben adatok áttelepítési útvonalak az SQL Server-beli virtuális gépen. Teljes körű áttelepítési lehetőségek és javaslatok, lásd: [-adatbázis áttelepítése az SQL Server-beli virtuális gépen](virtual-machines-windows-migrate-sql.md).
