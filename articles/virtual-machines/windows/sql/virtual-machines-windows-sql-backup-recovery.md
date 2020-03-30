---
title: Biztonsági mentés és visszaállítás az SQL Server szolgáltatáshoz az Azure virtuális gépein | Microsoft dokumentumok
description: Az Azure virtuális gépeken futó SQL Server-adatbázisok biztonsági mentésével és visszaállításával kapcsolatos szempontok ismertetése.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: e4c126bbac73accb984f1040a7fea1740d919233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249775"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Biztonsági mentés és visszaállítás Azure-beli SQL Server-alapú virtuális gépeken

Ez a cikk útmutatást nyújt az Azure-beli Windows virtuális gépen futó SQL Server biztonsági mentési és visszaállítási lehetőségeihez. Az Azure Storage az összes Azure virtuálisgép-lemez három példányát tartja karban az adatvesztés vagy a fizikai adatsérülés elleni védelem biztosítása érdekében. Így a helyszíni megoldásokkal ellentétben nem kell a hardverhibákra összpontosítania. Az SQL Server-adatbázisokról azonban továbbra is biztonsági másolatot kell készítenie az alkalmazás- vagy felhasználói hibák, például a véletlen adatbeszúrások vagy -törlések elleni védelem érdekében. Ebben a helyzetben fontos, hogy képes legyen visszaállítani egy adott időpontban.

A cikk első része áttekintést nyújt a rendelkezésre álló biztonsági mentési és visszaállítási lehetőségekről. Ezt olyan szakaszok követik, amelyek további információt nyújtanak az egyes stratégiákról.

## <a name="backup-and-restore-options"></a>Biztonsági mentési és visszaállítási beállítások

Az alábbi táblázat az Azure virtuális gépeken futó SQL Server különböző biztonsági mentési és visszaállítási lehetőségeiről tartalmaz tájékoztatást:

| Stratégia | SQL-verziók | Leírás |
|---|---|---|
| [Automatikus biztonsági mentés](#automated) | 2014<br/> 2016<br/> 2017 | Az automatikus biztonsági mentés lehetővé teszi, hogy rendszeres biztonsági mentéseket ütemezzen az SQL Server virtuális gép összes adatbázisáról. A biztonsági mentések legfeljebb 30 napig tárolódnak az Azure storage-ban. Az SQL Server 2016-tól kezdve az automatikus biztonsági mentés v2 további lehetőségeket kínál, például a manuális ütemezés konfigurálását, valamint a teljes és a naplóbiztonsági mentések gyakoriságát. |
| [Azure Backup SQL-alapú virtuális gépekhez](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | Az Azure Backup nagyvállalati szintű biztonsági mentési lehetőséget biztosít az Azure virtuális gépeken futó SQL Server számára. Ezzel a szolgáltatással központilag kezelheti a biztonsági mentések több kiszolgáló és több ezer adatbázis. Az adatbázisok visszaállíthatók a portál egy adott időpontjára. Kínál egy testreszabható adatmegőrzési szabályzat, amely évekig képes biztonsági mentések karbantartására. |
| [Kézi biztonsági mentés](#manual) | Összes | Az SQL Server verziójától függően különböző módszerek vannak az Azure virtuális rendszeren futó SQL Server manuális biztonsági mentésére és visszaállítására. Ebben a forgatókönyvben ön felelős az adatbázisok biztonsági mentésének módjáért, valamint a biztonsági mentések tárolási helyért és felügyeletéért. |

A következő szakaszok részletesebben ismertetik az egyes beállításokat. A cikk utolsó része egy jellemzőmátrix formájában tartalmaz összegzést.

## <a name="automated-backup"></a><a id="automated"></a>Automatikus biztonsági mentés

Az Automatikus biztonsági mentés automatikus biztonsági mentési szolgáltatást biztosít az Azure-beli Windows virtuális gépeken futó SQL Server Standard és Enterprise kiadásokhoz. Ezt a szolgáltatást az [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md)biztosítja, amely automatikusan telepítve van az SQL Server Windows virtuálisgép-lemezképeire az Azure Portalon.

Az összes adatbázisról egy Azure storage-fiók konfigurált biztonsági mentése történik. A biztonsági mentések legfeljebb 30 napig titkosíthatók és őrizhetők.

Az SQL Server 2016 és a magasabb szintű virtuális gépek több testreszabási lehetőséget kínálnak az Automatikus biztonsági mentés v2-vel. Ezek a fejlesztések a következők:

- Rendszeradatbázis biztonsági másolatai
- Kézi biztonsági mentés ütemezése és időablak
- Teljes és naplófájl biztonsági mentési gyakorisága

Adatbázis visszaállításához meg kell keresnie a szükséges biztonságimásolat-fájlt(oka)t a tárfiókban, és az SQL Server Management Studio (SSMS) vagy a Transact-SQL parancsokkal kell végrehajtania egy visszaállítást az SQL Virtuális gépen.

Az SQL virtuális gépek automatikus biztonsági mentésének konfigurálásáról az alábbi cikkekben talál további információt:

- **SQL Server 2016/2017**: [Automatikus biztonsági mentés v2 az Azure virtuális gépekhez](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Automatikus biztonsági mentés az SQL Server 2014 virtuális gépekhez](virtual-machines-windows-sql-automated-backup.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a>Azure Biztonsági mentés SQL virtuális gépekhez

[Az Azure Backup](/azure/backup/) nagyvállalati szintű biztonsági mentési lehetőséget biztosít az Azure virtuális gépeken futó SQL Server számára. Az összes biztonsági mentés tárolják és kezelik a Recovery Services-tárolóban. Ennek a megoldásnak számos előnye van, különösen a vállalkozások számára:

- **Infrastruktúra-biztonsági mentés:** Nem kell biztonsági másolat-kiszolgálókat vagy tárolóhelyeket kezelnie.
- **Méretezés:** Számos SQL virtuális gép és több ezer adatbázis védelme.
- **Használatalapú fizetés:** Ez a funkció az Azure Backup által biztosított külön szolgáltatás, de mint minden Azure-szolgáltatás esetében, csak a használatért kell fizetnie.
- **Központi felügyelet és figyelés:** Központilag kezelheti az összes biztonsági mentést, beleértve az Azure Backup által támogatott egyéb számítási feladatokat is, egyetlen irányítópultról az Azure-ban.
- **Házirend alapú biztonsági mentés és -megőrzés:** Hozzon létre szabványos biztonsági mentési házirendeket a rendszeres biztonsági mentések számára. Adatmegőrzési házirendek létrehozása a biztonsági mentések évekig való karbantartásához.
- **Az SQL Always On támogatása:** Az SQL Server Always On konfigurációjának észlelése és védelme, valamint a biztonsági mentés rendelkezésre állási csoport biztonsági mentési beállításainak tiszteletben tartásával.
- **15 perces helyreállítási pont célkitűzés (RPO)**: Az SQL tranzakciónapló biztonsági mentések konfigurálása 15 percenként.
- **Időpont-visszaállítás:** A portál segítségével az adatbázisok egy adott időpontban helyreállíthatók anélkül, hogy manuálisan vissza kellene állítania több teljes, különbözeti és naplóbiztonsági mentést.
- **Összesített e-mail riasztások hibák :** Konfigurálja az összevont e-mail értesítéseket az esetleges hibák.
- **Szerepköralapú hozzáférés-vezérlés**: Határozza meg, hogy ki kezelheti a biztonsági mentési és visszaállítási műveleteket a portálon keresztül.

A demóval együtt a következő videóból gyorsáttekintést kaphatsz arról, hogyan működik együtt:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Ez az Azure Backup megoldás sql virtuális gépek hez általában elérhető. További információt az [SQL Server-adatbázis biztonsági mentése az Azure-ba](../../../backup/backup-azure-sql-database.md)című témakörben talál.

## <a name="manual-backup"></a><a id="manual"></a>Kézi biztonsági mentés

Ha manuálisan szeretné kezelni a biztonsági mentési és visszaállítási műveleteket az SQL virtuális gépeken, a használt SQL Server verziójától függően számos lehetőség közül választhat. A biztonsági mentés és visszaállítás áttekintését az SQL Server verzióján alapuló alábbi cikkek ben találja:

- [Biztonsági mentés és visszaállítás az SQL Server 2016-os és újabb verzióihoz](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Biztonsági mentés és visszaállítás az SQL Server 2014-hez](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Biztonsági mentés és visszaállítás az SQL Server 2012-hez](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Biztonsági mentés és visszaállítás az SQL Server 2008 R2 rendszerhez](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Biztonsági mentés és visszaállítás az SQL Server 2008 rendszerhez](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

A következő szakaszok részletesebben ismertetik a manuális biztonsági mentési és visszaállítási lehetőségeket.

### <a name="backup-to-attached-disks"></a>Biztonsági mentés csatlakoztatott lemezekre

Az Azure virtuális gépeken futó SQL Server natív biztonsági mentési és visszaállítási technikákat használhat a virtuális gép csatlakoztatott lemezei használatával a biztonsági másolat fájlok céljához. Az Azure virtuális gépekhez csatolható lemezek száma azonban a virtuális gép [mérete](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)alapján korlátozva van. Van is a többlet-felső -ból korong vezetés -hoz tekint.

Ha például manuálisan hozhat létre teljes adatbázis-biztonsági másolatot az SQL Server Management Studio (SSMS) vagy a Transact-SQL használatával, olvassa el a [Teljes adatbázis biztonsági másolatának létrehozása című témakört.](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server)

### <a name="backup-to-url"></a>Biztonsági mentés URL-címre

Az SQL Server 2012 SP1 CU2 szervizcsomaggal kezdve közvetlenül biztonsági másolatot készíthet a Microsoft Azure Blob storage-ról, amely url-cím biztonsági mentésének is nevezik. Az SQL Server 2016 a következő fejlesztéseket is bevezette ehhez a szolgáltatáshoz:

| 2016-os fejlesztés | Részletek |
| --- | --- |
| **Csíkozás** |A Microsoft Azure blob storage-ra való biztonsági mentés során az SQL Server 2016 támogatja a biztonsági mentést több blobra, hogy nagy adatbázisokról is biztonsági másolatot készíthessenek, legfeljebb 12,8 TB-ig. |
| **Pillanatkép biztonsági mentése** |Az Azure-pillanatképek használatával az SQL Server File-Snapshot Backup szinte azonnali biztonsági mentéseket és gyors visszaállításokat biztosít az Azure Blob storage szolgáltatás használatával tárolt adatbázisfájlokhoz. Ez a funkció lehetővé teszi a biztonsági mentési és visszaállítási házirendek egyszerűsítését. A fájl-pillanatkép biztonsági mentése szintén támogatja a pont-visszaállítást. További információt az [Azure-beli adatbázisfájlok pillanatkép-biztonsági mentései című témakörben talál.](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) |

További információt az SQL Server verzióján alapuló alábbi cikkek ben talál:

- **SQL Server 2016/2017**: [SQL Server biztonsági másolat URL-címre](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 – URL-cím](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012 – URL-cím](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Felügyelt biztonsági mentés

Az SQL Server 2014-től kezdődően a felügyelt biztonsági mentés automatizálja a biztonsági mentések létrehozását az Azure storage-ba. A színfalak mögött a Felügyelt biztonsági mentés a cikk előző részében ismertetett Biztonsági mentés URL-címre funkciót használja. A felügyelt biztonsági mentés az SQL Server virtuális gép automatikus biztonsági mentési szolgáltatását is támogatja.

Az SQL Server 2016-tól kezdődően a felügyelt biztonsági mentés további lehetőségeket kapott az ütemezéshez, a rendszeradatbázis biztonsági mentéséhez, valamint a teljes és a napló biztonsági mentési gyakoriságához.

További információt az SQL Server verzióján alapuló alábbi cikkekben talál:

- [Felügyelt biztonsági mentés a Microsoft Azure-ba az SQL Server 2016-os és újabb verzióihoz](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Felügyelt biztonsági mentés a Microsoft Azure for SQL Server 2014 rendszerhez](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Döntési mátrix

Az alábbi táblázat összefoglalja az Azure-beli SQL Server virtuális gépek biztonsági mentési és visszaállítási lehetőségeinek képességeit.

|| **Automatikus biztonsági mentés** | **Azure Backup for SQL** | **Kézi biztonsági mentés** |
|---|---|---|---|
| További Azure-szolgáltatást igényel |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Biztonsági mentési szabályzat konfigurálása az Azure Portalon | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Adatbázisok visszaállítása az Azure Portalon |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Több kiszolgáló kezelése egy irányítópulton |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Adott időpontnak megfelelő helyreállítás | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15 perces helyreállítási pont célkitűzés (RPO) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Rövid távú biztonsági mentési adatmegőrzési házirend (nap) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Hosszú távú biztonsági mentési adatmegőrzési házirend (hónapok, évek) |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Beépített támogatás az SQL Server mindig bekapcsolva |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Biztonsági mentés az Azure Storage-fiók(ok) számára | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatikus) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatikus) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(ügyfél által kezelt) |
| A tároló- és biztonságimentési fájlok kezelése | | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Biztonsági mentés a virtuális gép csatlakoztatott lemezeihez |   |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Központi, testre szabható biztonsági mentési jelentések |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Összesített e-mail riasztások hibák esetén |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Figyelés testreszabása az Azure Monitor naplói alapján |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Biztonsági mentési feladatok figyelése SSMS vagy Transact-SQL parancsfájlokkal | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Adatbázisok visszaállítása SSMS vagy Transact-SQL parancsfájlokkal | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>További lépések

Ha az SQL Server üzembe helyezését egy Azure virtuális gépen tervezi, a következő útmutatóban talál kiépítési útmutatót: [Windows SQL Server virtuális gép kiépítése az Azure Portalon.](virtual-machines-windows-portal-sql-server-provision.md)

Bár a biztonsági mentés és visszaállítás az adatok áttelepítéséhez használható, az Azure virtuális gépEN az SQL Server-re való adatáttelepítési útvonalak potenciálisan egyszerűbbek lehetnek. Az áttelepítési lehetőségek és javaslatok teljes körű áttekintését az [Adatbázis áttelepítése az SQL Server kiszolgálóra Azure-beli virtuális gépen című témakörben ismerteti.](virtual-machines-windows-migrate-sql.md)
