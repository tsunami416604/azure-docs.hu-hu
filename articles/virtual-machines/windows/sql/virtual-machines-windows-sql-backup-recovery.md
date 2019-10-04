---
title: Azure-beli virtuális gépek SQL Serverának biztonsági mentése és visszaállítása | Microsoft Docs
description: Az Azure Virtual Machines-on futó SQL Server adatbázisok biztonsági mentési és visszaállítási szempontjait ismerteti.
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
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100553"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Biztonsági mentés és visszaállítás Azure-beli SQL Server-alapú virtuális gépeken

Ez a cikk útmutatást nyújt az Azure-beli Windows rendszerű virtuális gépeken futó SQL Server számára elérhető biztonsági mentési és visszaállítási lehetőségekről. Az Azure Storage minden Azure-beli virtuális gép lemezét három példányban tárolja az adatvesztés vagy a fizikai adatsérülés elleni védelem biztosításához. Így a helyszíni rendszertől eltérően nem kell a hardver meghibásodására összpontosítania. Azonban továbbra is biztonsági másolatot kell készítenie SQL Server adatbázisairól az alkalmazás vagy a felhasználói hibák, például a véletlen adatbeszúrások vagy a törlések elleni védelem érdekében. Ebben az esetben fontos, hogy egy adott időpontra lehessen visszaállítani.

A cikk első része áttekintést nyújt a rendelkezésre álló biztonsági mentési és visszaállítási lehetőségekről. Ezeket a szakaszokat követve további információkat adhat meg az egyes stratégiákról.

## <a name="backup-and-restore-options"></a>Biztonsági mentési és visszaállítási beállítások

Az alábbi táblázat az Azure-beli virtuális gépeken futó SQL Server különböző biztonsági mentési és visszaállítási lehetőségeiről nyújt információkat:

| Stratégia | SQL-verziók | Leírás |
|---|---|---|
| [Automatikus biztonsági mentés](#automated) | 2014<br/> 2016<br/> 2017 | Az automatikus biztonsági mentés lehetővé teszi a SQL Server VM összes adatbázisának rendszeres biztonsági mentését. A biztonsági mentések tárolása az Azure Storage-ban akár 30 napig is eltartható. A SQL Server 2016-es verziójától kezdve az automatizált Backup v2 további lehetőségeket kínál, például a manuális ütemezés konfigurálását, valamint a teljes és naplózott biztonsági másolatok gyakoriságát. |
| [Azure Backup SQL-alapú virtuális gépekhez](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | A Azure Backup nagyvállalati szintű biztonsági mentési képességet biztosít az Azure-beli virtuális gépeken futó SQL Server számára. Ezzel a szolgáltatással központilag kezelheti a biztonsági mentéseket több kiszolgáló és több ezer adatbázis között. Az adatbázisok visszaállíthatók egy adott időpontban a portálon. Testreszabható adatmegőrzési szabályzatot kínál, amely évekig képes biztonsági mentéseket fenntartani. |
| [Manuális biztonsági mentés](#manual) | Összes | A SQL Server verziójától függően számos módszer áll rendelkezésre az Azure-beli virtuális gépen futó SQL Server manuális biztonsági mentésére és visszaállítására. Ebben az esetben Ön felelős az adatbázisok biztonsági mentésének, valamint a biztonsági másolatok tárolási helyének és kezelésének módjáról. |

Az alábbi szakaszok részletesebben ismertetik az egyes lehetőségeket. A cikk utolsó szakasza egy szolgáltatás-mátrix formájában összefoglalást tartalmaz.

## <a id="automated"></a>Automatikus biztonsági mentés

Az automatikus biztonsági mentés automatikus biztonsági mentési szolgáltatást biztosít az Azure-beli Windows rendszerű virtuális gépeken futó SQL Server Standard és Enterprise kiadásokhoz. Ezt a szolgáltatást a [SQL Server IaaS-ügynök bővítménye](virtual-machines-windows-sql-server-agent-extension.md)nyújtja, amely a Azure Portal SQL Server Windows rendszerű virtuálisgép-lemezképeken automatikusan települ.

Az összes adatbázis biztonsági mentése egy Ön által konfigurált Azure Storage-fiókba történik. A biztonsági mentések titkosítva és akár 30 napig is megmaradnak.

A SQL Server 2016-es és újabb virtuális gépek további testreszabási lehetőségeket kínálnak az automatizált Backup v2-mel. A következő fejlemények a következők:

- Rendszeradatbázis biztonsági mentései
- Manuális biztonsági mentés időpontja és időablaka
- Teljes és naplófájlok biztonsági mentésének gyakorisága

Egy adatbázis visszaállításához meg kell keresnie a szükséges biztonságimásolat-fájlokat a Storage-fiókban, és el kell végeznie az SQL-alapú virtuális gép visszaállítását SQL Server Management Studio (SSMS) vagy Transact-SQL parancs használatával.

Az SQL virtuális gépek automatikus biztonsági mentésének konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkek egyikét:

- **SQL Server 2016/2017**: [Automatizált Backup v2 Azure-Virtual Machines](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Automatikus biztonsági mentés a SQL Server 2014 Virtual Machines](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a>Azure Backup SQL virtuális gépekhez

A [Azure Backup](/azure/backup/) nagyvállalati szintű biztonsági mentési képességet biztosít az Azure-beli virtuális gépeken futó SQL Server számára. Az összes biztonsági mentést egy Recovery Services tárolóban tárolják és kezelik. A megoldás számos előnnyel jár, különösen a vállalatok számára:

- **Zéró infrastruktúra biztonsági mentése**: Nem kell kezelnie a biztonságimásolat-kiszolgálókat és a tárolási tárolóhelyeket.
- **Skála**: Számos SQL virtuális gép és több ezer adatbázis védelmének biztosítása.
- **Pay-As-You-Go**: Ez a funkció Azure Backup által biztosított különálló szolgáltatás, de az összes Azure-szolgáltatáshoz hasonlóan csak a ténylegesen használt funkciókért kell fizetnie.
- **Központi felügyelet és monitorozás**: Központilag kezelheti az összes biztonsági mentést, beleértve a Azure Backup által támogatott egyéb munkaterheléseket az Azure egyetlen irányítópultján.
- **Házirend által vezérelt biztonsági mentés és megőrzés**: Normál biztonsági mentési szabályzatok létrehozása a rendszeres biztonsági mentésekhez. Adatmegőrzési szabályzatokat hozhat létre a biztonsági mentések évekig történő fenntartásához.
- **Az SQL AlwaysOn támogatása**: A SQL Server always on Configuration és a biztonsági mentési rendelkezésre állási csoport biztonsági mentési beállításainak felismerése és biztosítása.
- **15 perces helyreállítási pont célkitűzése (RPO)** : SQL-tranzakciós naplók biztonsági másolatainak konfigurálása 15 percenként.
- **Visszaállítás időpontja**: A portál használatával állíthatja helyre az adatbázisokat egy adott időpontra anélkül, hogy manuálisan vissza kellene állítania a több teljes, differenciált és naplózott biztonsági mentést.
- **Összevont e-mail-riasztások a hibákhoz**: Összevont e-mail-értesítések konfigurálása bármilyen hiba esetén.
- **Szerepköralapú hozzáférés-vezérlés**: Határozza meg, hogy kik kezelhetik a biztonsági mentési és visszaállítási műveleteket a portálon.

Az alábbi videóban gyorsan áttekintheti, hogyan működik együtt a bemutatóval:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Az SQL virtuális gépekre vonatkozó Azure Backup megoldás általánosan elérhető. További információ: [SQL Server adatbázis biztonsági mentése az Azure-](../../../backup/backup-azure-sql-database.md)ba.

## <a id="manual"></a>Manuális biztonsági mentés

Ha manuálisan szeretné kezelni a biztonsági mentési és visszaállítási műveleteket az SQL-alapú virtuális gépeken, a használt SQL Server verziójától függően több lehetőség is rendelkezésre áll. A biztonsági mentés és a visszaállítás áttekintéséhez tekintse meg az alábbi cikkek egyikét a SQL Server verziója alapján:

- [Biztonsági mentés és visszaállítás a SQL Server 2016-es és újabb verzióiban](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [SQL Server 2014 biztonsági mentése és visszaállítása](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [SQL Server 2012 biztonsági mentése és visszaállítása](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Biztonsági mentés és visszaállítás a SQL Server SQL Server 2008 R2 rendszerhez](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [SQL Server 2008 biztonsági mentése és visszaállítása](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

A következő szakaszok részletesen ismertetik a manuális biztonsági mentési és visszaállítási lehetőségeket.

### <a name="backup-to-attached-disks"></a>Biztonsági mentés csatolt lemezekre

Az Azure-beli virtuális gépeken futó SQL Server esetén natív biztonsági mentési és visszaállítási technikákat használhat a virtuális gépen lévő csatlakoztatott lemezek használatával a biztonságimásolat-fájlok céljához. A [virtuális gép mérete](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)alapján azonban korlátozva van az Azure-beli virtuális gépekhez kapcsolható lemezek száma. A Lemezkezelés szolgáltatással kapcsolatos szempontokat is figyelembe kell venni.

A teljes adatbázis biztonsági mentésének a SQL Server Management Studio (SSMS) vagy a Transact-SQL használatával történő manuális létrehozásával kapcsolatos példát a [teljes adatbázis biztonsági másolatának létrehozása](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server)című témakörben talál.

### <a name="backup-to-url"></a>Biztonsági mentés az URL-címre

A SQL Server 2012 SP1 CU2 kezdve a biztonsági mentést és visszaállítást közvetlenül Microsoft Azure Blob Storage-ba, amely az URL-re való biztonsági mentés néven is ismert. SQL Server 2016 a következő fejlesztéseket is bemutatta ehhez a szolgáltatáshoz:

| 2016-fejlesztés | Részletek |
| --- | --- |
| **Csíkozás** |Ha Microsoft Azure Blob Storage-ba készít biztonsági másolatot, SQL Server 2016 támogatja a több blobra történő biztonsági mentést, így a nagyméretű adatbázisok biztonsági mentését legfeljebb 12,8 TB-ig engedélyezheti. |
| **Pillanatkép biztonsági mentése** |Az Azure-Pillanatképek használatával SQL Server fájl – a pillanatképek biztonsági mentése majdnem azonnali biztonsági mentést és gyors visszaállítást biztosít az Azure Blob Storage szolgáltatással tárolt adatbázisfájlok számára. Ez a funkció lehetővé teszi a biztonsági mentési és visszaállítási házirendek egyszerűsítését. A fájl – pillanatkép biztonsági mentése az időponthoz tartozó visszaállítást is támogatja. További információ: az [Azure-beli adatbázisfájlok biztonsági mentései](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

További információkért tekintse meg az alábbi cikkek egyikét a SQL Server verziója alapján:

- **SQL Server 2016/2017**: [SQL Server biztonsági mentés az URL-címre](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [SQL Server 2014 biztonsági mentés URL-címre](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [SQL Server 2012 biztonsági mentés URL-címre](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Felügyelt biztonsági mentés

A SQL Server 2014-es verziójától kezdve a felügyelt biztonsági mentés automatizálja az Azure Storage-ba történő biztonsági másolatok létrehozását. A háttérben a felügyelt biztonsági mentés a jelen cikk előző szakaszában ismertetett, a biztonsági mentés URL-címére szolgáló funkciót használja. A felügyelt biztonsági mentés a mögöttes szolgáltatás, amely támogatja a SQL Server VM automatikus biztonsági mentési szolgáltatást.

A SQL Server 2016-es verziójától kezdve a felügyelt biztonsági mentés további lehetőségeket kínál az ütemezéshez, a rendszeradatbázis biztonsági mentéséhez, valamint a teljes és naplózott biztonsági mentés gyakoriságához

További információkért tekintse meg az alábbi cikkek egyikét a SQL Server verziója alapján:

- [Felügyelt biztonsági mentés Microsoft Azure SQL Server 2016-es és újabb verziókban](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Felügyelt biztonsági mentés a Microsoft Azure SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Döntési mátrix

Az alábbi táblázat összefoglalja az Azure-ban SQL Server virtuális gépek biztonsági mentésének és visszaállításának funkcióit.

|| **Automatikus biztonsági mentés** | **SQL-Azure Backup** | **Manuális biztonsági mentés** |
|---|---|---|---|
| További Azure-szolgáltatásra van szükség |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Biztonsági mentési szabályzat konfigurálása Azure Portal | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Adatbázisok visszaállítása Azure Portal |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Több kiszolgáló kezelése egyetlen irányítópulton |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Adott időpontnak megfelelő helyreállítás | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15 perces helyreállítási pont célkitűzése (RPO) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Rövid távú biztonsági mentési adatmegőrzési szabályzat (nap) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Biztonsági másolatok hosszú távú megőrzési szabályzata (hónapok, évek) |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Beépített támogatás a SQL Server always on |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Biztonsági mentés az Azure Storage-fiók (ok) ba | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png)automatikus | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png)automatikus | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(ügyfél által felügyelt) |
| A tárolási és a biztonságimásolat-fájlok kezelése | | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Biztonsági másolat készítése a virtuális gépen található csatlakoztatott lemezekre |   |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Központi testreszabható biztonsági mentési jelentések |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Összevont e-mail-riasztások a hibákhoz |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| A figyelés testreszabása Azure Monitor naplók alapján |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Biztonsági mentési feladatok figyelése SSMS vagy Transact-SQL-parancsfájlok segítségével | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Adatbázisok visszaállítása SSMS vagy Transact-SQL-parancsfájlokkal | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Igen](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>További lépések

Ha SQL Server Azure-beli virtuális gépen történő üzembe helyezését tervezi, a következő útmutatóban talál kiépítési útmutatót: [Windows SQL Server virtuális gép kiépítése a Azure Portalban](virtual-machines-windows-portal-sql-server-provision.md).

Bár a biztonsági mentés és a visszaállítás használható az adatai áttelepítésére, az Azure-beli virtuális gépeken SQL Server lehet az adatáttelepítési útvonalak is. Az áttelepítési lehetőségek és javaslatok teljes körű megvitatását lásd: [adatbázis áttelepítése SQL Server Azure-beli virtuális gépen](virtual-machines-windows-migrate-sql.md).
