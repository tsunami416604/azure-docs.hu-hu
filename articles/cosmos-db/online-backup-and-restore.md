---
title: Online biztonsági mentés és igény szerinti adat-visszaállítás az Azure Cosmos DB-ben
description: Ez a cikk bemutatja, hogyan működik az automatikus, online biztonsági mentés és az igény szerinti adat-visszaállítás az Azure Cosmos DB-ben.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: f5cc4339d7d6dce6d49c8d3eb744fca7fa5774d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240431"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Online biztonsági mentés és igény szerinti adat-visszaállítás az Azure Cosmos DB-ben

Az Azure Cosmos DB automatikusan rendszeres időközönként biztonsági mentést készít az adatokról. Az automatikus biztonsági mentések az adatbázis-műveletek teljesítményének vagy rendelkezésre állásának befolyásolása nélkül készülnek. Az összes biztonsági mentés külön tárolja a tárolási szolgáltatásban, és ezek a biztonsági mentések globálisan replikálódik a regionális katasztrófák elleni rugalmasság érdekében. Az automatikus biztonsági mentések hasznos a forgatókönyvek, amikor véletlenül törli vagy frissíti az Azure Cosmos-fiók, adatbázis vagy tároló, és később az adatok helyreállítását.

## <a name="automatic-and-online-backups"></a>Automatikus és online biztonsági mentések

Az Azure Cosmos DB-vel nem csak az adatok, hanem az adatok biztonsági mentései is rendkívül redundánsak és rugalmasak a regionális katasztrófákkal szemben. A következő lépések bemutatják, hogyan hajtja végre az Azure Cosmos DB az adatok biztonsági mentését:

* Az Azure Cosmos DB 4 óránként vagy egy adott időpontban automatikusan biztonsági másolatot készít az adatbázisáról, és csak a két legutóbbi biztonsági másolatot őrzi meg. Tároló vagy adatbázis törlése esetén azonban az Azure Cosmos DB 30 napig megőrzi az adott tároló vagy adatbázis meglévő pillanatképeit.

* Az Azure Cosmos DB ezeket a biztonsági mentéseket az Azure Blob storage-ban tárolja, míg a tényleges adatok az Azure Cosmos DB-n belül találhatók.

*  Az alacsony késés biztosítása érdekében a biztonsági másolat pillanatképe az Azure Blob storage-ban ugyanabban a régióban, mint az aktuális írási régió (vagy az egyik írási régiók, abban az esetben, ha egy több fős konfiguráció) az Azure Cosmos adatbázis-fiók. A regionális katasztrófa elleni rugalmasság érdekében az Azure Blob storage biztonsági mentési adatainak minden pillanatképe ismét replikálódik egy másik régióba georedundáns tárolás (GRS) révén. A régió, amelyre a biztonsági mentés replikált alapul a forrásrégió és a regionális pár társított forrásrégióban. További információkért tekintse meg az [Azure-régiók georedundáns párjainak listáját.](../best-practices-availability-paired-regions.md) Ez a biztonsági másolat közvetlenül nem érhető el. Az Azure Cosmos DB csak akkor használja ezt a biztonsági mentést, ha biztonsági mentési visszaállítást kezdeményez.

* A biztonsági mentések az alkalmazás teljesítményének vagy rendelkezésre állásának befolyásolása nélkül készülnek. Az Azure Cosmos DB adatmentést hajt végre a háttérben anélkül, hogy további kiosztott átviteli -t (RUs) használna, vagy befolyásolná az adatbázis teljesítményét és rendelkezésre állását.

* Ha véletlenül törölte vagy megrongálta az adatokat, 8 órán belül forduljon az [Azure-támogatáshoz,](https://azure.microsoft.com/support/options/) hogy az Azure Cosmos DB csapata segíthessen az adatok visszaállításában a biztonsági mentésekből.

Az alábbi képen látható, hogy az USA nyugati részén található három elsődleges fizikai partícióval rendelkező Azure Cosmos-tárolóról készül biztonsági másolat egy távoli Azure Blob Storage-fiókban az USA nyugati részén, majd replikálja az USA keleti részébe:

![A GRS Azure Storage összes Cosmos DB-entitásának rendszeres teljes biztonsági mentése](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>A saját biztonsági mentések kezelésének lehetőségei

Az Azure Cosmos DB SQL API-fiókokkal saját biztonsági mentéseket is karbantarthat az alábbi módszerek egyikével:

* Az [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) segítségével rendszeres időközönként áthelyezheti az adatokat egy ön által kiválasztott tárolóba.

* Az Azure Cosmos DB [módosítási hírcsatornájával](change-feed.md) rendszeres időközönként olvashatadatokat a teljes biztonsági mentések, valamint a növekményes módosítások, és tárolja a saját tárolójában.

## <a name="backup-retention-period"></a>Biztonsági mentés megőrzési időszaka

Az Azure Cosmos DB négyóránként pillanatképeket készít az adatokról. Adott időpontban csak az utolsó két pillanatkép marad meg. Tároló vagy adatbázis törlése esetén azonban az Azure Cosmos DB 30 napig megőrzi az adott tároló vagy adatbázis meglévő pillanatképeit.

## <a name="restoring-data-from-online-backups"></a>Adatok visszaállítása online biztonsági mentésekből

Az adatok véletlen törlése vagy módosítása az alábbi esetekben fordulhat elő:  

* A teljes Azure Cosmos-fiók törlődik

* Egy vagy több Azure Cosmos-adatbázis törlődik

* Egy vagy több Azure Cosmos-tároló törlődik

* Az Azure Cosmos-elemek (például dokumentumok) egy tárolóban törlődnek vagy módosulnak. Ezt a konkrét esetet általában "adatsérülésnek" nevezik.

* A megosztott ajánlat-adatbázis vagy -tárolók törlődnek vagy sérültek

Az Azure Cosmos DB a fenti forgatókönyvekben visszaállíthatja az adatokat. A visszaállítási folyamat mindig új Azure Cosmos-fiókot hoz létre a visszaállított adatok tárolásához. Az új fiók neve, ha nincs megadva, `<Azure_Cosmos_account_original_name>-restored1`a formátuma meg lesz adva. Az utolsó számjegy növekszik, ha több visszaállítást kísérel meg. Nem állíthatja vissza az adatokat egy előre létrehozott Azure Cosmos-fiókba.

Ha egy Azure Cosmos-fiók törlődik, visszaállíthatjuk az adatokat egy fiókot az azonos nevű, feltéve, hogy a fiók neve nincs használatban. Ilyen esetekben javasoljuk, hogy ne hozza létre újra a fiókot a törlés után, mert nem csak megakadályozza, hogy a visszaállított adatok ugyanazt a nevet használják, hanem megnehezíti a megfelelő fiók felderítését a visszaállításhoz. 

Egy Azure Cosmos-adatbázis törlésekor lehetséges a teljes adatbázis vagy az adatbázison belüli tárolók egy részhalmazának visszaállítása. Az is lehetséges, hogy az adatbázisok között tárolók at, és állítsa vissza őket, és az összes visszaállított adatok egy új Azure Cosmos-fiókba kerül.

Ha egy tárolón belül egy vagy több elemet véletlenül törölnek vagy módosítanak (az adatsérülésesetet), meg kell adnia a visszaállítás idejét. Az idő a lényeg ebben az ügyben. Mivel a tároló élő, a biztonsági mentés továbbra is fut, így ha a megőrzési időszakon túl (az alapértelmezett érték nyolc óra) túl vár, a biztonsági mentések felülíródnak. Törlések esetén az adatok már nem tárolódnak, mert a biztonsági mentési ciklus nem írja felül őket. A törölt adatbázisok vagy tárolók biztonsági másolatait a rendszer 30 napig menti.

Ha az átviteli folyamat az adatbázis szintjén (azaz ahol egy tárolókészlet osztja meg a kiosztott átviteli sebességgel), a biztonsági mentési és visszaállítási folyamat ebben az esetben a teljes adatbázis szintjén történik, és nem az egyes tárolók szintjén. Ilyen esetekben a visszaállítandó tárolók egy részhalmazának kiválasztása nem lehetséges.

## <a name="migrating-data-to-the-original-account"></a>Adatok áttelepítése az eredeti fiókba

Az adat-visszaállítás elsődleges célja, hogy módot adjon a véletlenül törölt vagy módosított adatok helyreállítására. Ezért azt javasoljuk, hogy először vizsgálja meg a helyreállított adatok tartalmát, hogy megbizonyosodjon arról, hogy azok a várt adatokat tartalmazzák. Ezután az adatok elsődleges fiókba való visszaáttelepítésének továbbáttelepítésén dolgozzon. Bár a visszaállított fiók használható élő fiókként, ez nem ajánlott lehetőség, ha éles számítási feladatok.  

Az alábbiakban az adatok eredeti Azure Cosmos-fiókba való visszaáttelepítésének különböző módjai:

* A [Cosmos DB adatáttelepítési eszközének használata](import-data.md)
* Az [Azure Data Factory használata]( ../data-factory/connector-azure-cosmos-db.md)
* [Módosítási hírcsatorna](change-feed.md) használata az Azure Cosmos DB-ben 
* Egyéni kód írása

Törölje a visszaállított fiókokat, amint végzett az áttelepítés, mert azok folyamatos díjakat vonnak maga után.

## <a name="next-steps"></a>További lépések

Ezután megtudhatja, hogyan állíthatja vissza az adatokat egy Azure Cosmos-fiókból, illetve hogyan telepítheti át az adatokat egy Azure Cosmos-fiókba

* Visszaállítási kérelem benyújtásához lépjen kapcsolatba az Azure-támogatással, [kérjen jegyet az Azure Portalról](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Adatok visszaállítása Azure Cosmos-fiókból](how-to-backup-and-restore.md)
* [A Cosmos DB módosítási hírcsatornájával](change-feed.md) áthelyezheti az adatokat az Azure Cosmos DB-be.
* [Az Azure Data Factory segítségével](../data-factory/connector-azure-cosmos-db.md) áthelyezheti az adatokat az Azure Cosmos DB-be.

