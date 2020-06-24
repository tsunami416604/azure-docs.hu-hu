---
title: Online biztonsági mentési és igény szerinti adatvisszaállítás Azure Cosmos DB
description: Ez a cikk bemutatja, hogyan működik az automatikus, az online biztonsági mentés és az igény szerinti adatvisszaállítás a Azure Cosmos DBban.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8ed9e23b178b8eeefbd3c3a690491124e6901180
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2020
ms.locfileid: "85112922"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Online biztonsági mentési és igény szerinti adatvisszaállítás Azure Cosmos DB

A Azure Cosmos DB rendszeres időközönként automatikusan biztonsági másolatot készít az adatairól. Az automatikus biztonsági mentések az adatbázis-műveletek teljesítményének vagy rendelkezésre állásának befolyásolása nélkül hozhatók. Az összes biztonsági mentés külön tárolódik a tárolási szolgáltatásokban, és ezek a biztonsági másolatok globálisan replikálódnak a regionális katasztrófák elleni rugalmasságra. Az automatikus biztonsági mentések olyan helyzetekben hasznosak, amikor véletlenül törli vagy frissíti az Azure Cosmos-fiókot,-adatbázist vagy-tárolót, és később szükség van az adatok helyreállítására.

## <a name="automatic-and-online-backups"></a>Automatikus és online biztonsági mentés

A Azure Cosmos DB, nem csupán az adatai, hanem az adatbiztonsági másolatok is nagyon redundánsak, és a regionális katasztrófák miatt rugalmasak. A következő lépések bemutatják, hogyan végzi el a Azure Cosmos DB az adatok biztonsági mentését:

* Az Azure Cosmos DB 4 óránként vagy egy adott időpontban automatikusan biztonsági másolatot készít az adatbázisáról, és csak a két legutóbbi biztonsági másolatot őrzi meg. Tároló vagy adatbázis törlése esetén azonban az Azure Cosmos DB 30 napig megőrzi az adott tároló vagy adatbázis meglévő pillanatképeit.

* Azure Cosmos DB tárolja ezeket a biztonsági másolatokat az Azure Blob Storage-ban, míg a tényleges adatok helyileg, Azure Cosmos DB belül találhatók.

*  Az alacsony késés garantálása érdekében a biztonsági mentés pillanatképét az Azure Blob Storage tárolja ugyanabban a régióban, mint az aktuális írási régiót (vagy az írási régiók egyikét), ha az Azure Cosmos Database-fiókja több főkiszolgálós konfigurációval rendelkezik. A regionális katasztrófák elleni rugalmasság érdekében az Azure Blob Storage-ban tárolt biztonsági mentési adatok minden pillanatképét újra replikálja egy másik régióba a Geo-redundáns tárolás (GRS) használatával. Az a régió, amelybe a biztonsági mentés replikálódik, a forrás-és a forrásoldali régióhoz társított regionális pár alapján történik. További információért tekintse meg a [geo-redundáns párok listáját az Azure-régiókról](../best-practices-availability-paired-regions.md) szóló cikkben. Ez a biztonsági másolat nem érhető el közvetlenül. Azure Cosmos DB ezt a biztonsági mentést csak akkor fogja használni, ha a biztonsági másolat visszaállítása megkezdődött.

* A biztonsági mentéseket az alkalmazás teljesítményének vagy rendelkezésre állásának befolyásolása nélkül kell elvégezni. Azure Cosmos DB végrehajtja az adatok biztonsági mentését a háttérben anélkül, hogy további kiosztott átviteli sebességet (RUs) kellene használnia, és nem befolyásolja az adatbázis teljesítményét és rendelkezésre állását.

* Ha véletlenül törölte vagy megsérült az adatai, akkor 8 órán belül kapcsolatba kell lépnie az [Azure támogatási szolgálatával](https://azure.microsoft.com/support/options/) , hogy a Azure Cosmos db csapat segítséget nyújtson a biztonsági másolatokból származó adatok visszaállításához.

Az alábbi képen látható, hogy az USA nyugati régiójában lévő három elsődleges fizikai partícióval rendelkező Azure Cosmos-tároló hogyan készül biztonsági mentésben egy távoli Azure Blob Storage-fiókban az USA nyugati régiójában, majd replikálva az USA keleti régiójába:

:::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="Az Azure Storage GRS összes Cosmos DB entitásának rendszeres teljes biztonsági mentése" border="false":::

## <a name="options-to-manage-your-own-backups"></a>A saját biztonsági mentések kezelésére szolgáló beállítások

Azure Cosmos DB SQL API-fiókokkal a következő módszerek egyikével is megőrizheti saját biztonsági mentését:

* A [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) használatával időnként áthelyezheti az adatátvitelt az Ön által választott tárhelyre.

* A teljes biztonsági mentéshez, valamint a növekményes változtatásokhoz és a saját tárolóban történő tárolásához használja a Azure Cosmos DB [módosítási csatornát](change-feed.md) .

## <a name="backup-retention-period"></a>Biztonsági másolat megőrzési ideje

A Azure Cosmos DB négy óránként pillanatképeket küld az adatairól. Egy adott időpontban csak az utolsó két pillanatképet őrzi meg a rendszer. Tároló vagy adatbázis törlése esetén azonban az Azure Cosmos DB 30 napig megőrzi az adott tároló vagy adatbázis meglévő pillanatképeit.

## <a name="restoring-data-from-online-backups"></a>Adatok visszaállítása online biztonsági mentésből

A véletlen törlés vagy az adatok módosítása a következő esetekben fordulhat elő:  

* A teljes Azure Cosmos-fiók törölve

* Egy vagy több Azure Cosmos-adatbázis törölve

* Egy vagy több Azure Cosmos-tároló törölve

* A tárolóban lévő Azure Cosmos-elemek (például dokumentumok) törlődnek vagy módosulnak. Ezt a konkrét esetet általában adatsérülésnek nevezzük.

* Egy megosztott ajánlat-adatbázis vagy-tároló egy megosztott ajánlati adatbázisban törölve vagy sérült

A Azure Cosmos DB az összes fenti helyzetben képes visszaállítani az adatok visszaállítását. A visszaállítási folyamat mindig új Azure Cosmos-fiókot hoz létre a visszaállított adatok tárolásához. Ha nincs megadva az új fiók neve, a formátuma lesz `<Azure_Cosmos_account_original_name>-restored1` . Az utolsó számjegy megnő, ha több visszaállítást próbálnak meg. Egy előre létrehozott Azure Cosmos-fiókba nem állíthatók vissza az adathalmazok.

Ha töröl egy Azure Cosmos-fiókot, visszaállíthatja az adathalmazt egy azonos nevű fiókba, ha a fiók neve nincs használatban. Ilyen esetekben azt javasoljuk, hogy a törlés után ne hozza újra létre a fiókot, mert a visszaállított adatok nem csupán ugyanazt a nevet használják, hanem a megfelelő fiók felfedését is lehetővé teszi a nehezebb helyreállításhoz. 

Ha töröl egy Azure Cosmos-adatbázist, a teljes adatbázist vagy a tárolók egy részhalmazát is visszaállíthatja az adott adatbázison belül. A tárolókat az adatbázisok között is kiválaszthatja, és visszaállíthatja őket, és az összes visszaállított adattal egy új Azure Cosmos-fiókba kerül.

Ha a tárolóban lévő egy vagy több elem véletlenül törölve vagy módosítva van (az adatsérülési eset), akkor meg kell adnia a visszaállításhoz szükséges időt. Ebben az esetben az idő lényege. Mivel a tároló él, a biztonsági mentés továbbra is fut, így ha a megőrzési időszakon túl várakozik (az alapértelmezett érték nyolc óra), a rendszer felülírja a biztonsági mentéseket. Törlés esetén az adatai már nem tárolódnak, mert a biztonsági mentési ciklus nem írja felül őket. A törölt adatbázisok vagy tárolók biztonsági másolatait 30 napig menti a rendszer.

Ha az átviteli sebességet az adatbázis szintjén adja meg (azaz a tárolók egy készlete osztozik a kiosztott átviteli sebességen), a biztonsági mentési és visszaállítási folyamat ebben az esetben a teljes adatbázis szintjén történik, és nem az egyes tárolók szintjén. Ilyen esetekben a visszaállítandó tárolók egy részhalmazának kiválasztása nem választható.

## <a name="migrating-data-to-the-original-account"></a>Az adatbázis áttelepítése az eredeti fiókba

Az adatok visszaállításának elsődleges célja, hogy lehetővé teszi a véletlenül törölt vagy módosított adatok helyreállítását. Ezért javasoljuk, hogy először vizsgálja meg a helyreállított adatok tartalmát, hogy biztosan tartalmazza a várt adatokat. Ezután végezze el az adatáttelepítést az elsődleges fiókba. Habár a visszaállított fiókot élő fiókként is használhatja, ez nem ajánlott megoldás, ha éles számítási feladatokkal rendelkezik.  

Az alábbi módokon térhet vissza az eredeti Azure Cosmos-fiókba:

* Az [Cosmos db adatáttelepítési eszköz](import-data.md) használata
* [Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md) használata
* [Módosítási hírcsatorna](change-feed.md) használata Azure Cosmos db 
* Egyéni kód írása

Ha befejezte az áttelepítést, törölje a visszaállított fiókokat, mivel ezek után folyamatos költségek merülnek fel.

## <a name="next-steps"></a>További lépések

A következő témakörből megtudhatja, hogyan állíthatja vissza az Azure Cosmos-fiók adatait, vagy megismerheti az adatok áttelepítését egy Azure Cosmos-fiókba

* A visszaállítási kérelem elvégzéséhez forduljon az Azure ügyfélszolgálatához, és kérjen [jegyet a Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Adatok visszaállítása Azure Cosmos-fiókból](how-to-backup-and-restore.md)
* Az adatAzure Cosmos DBba való áthelyezéshez [használja a Cosmos db módosítási csatornát](change-feed.md) .
* A [Azure Data Factory használatával](../data-factory/connector-azure-cosmos-db.md) viheti át az adatAzure Cosmos DBba.

