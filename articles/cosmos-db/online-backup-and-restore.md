---
title: Online biztonsági mentési és igény szerinti adatvisszaállítás Azure Cosmos DB
description: Ez a cikk azt ismerteti, hogyan működik az automatikus biztonsági mentés, az igény szerinti adatok visszaállítása, hogyan konfigurálható a biztonsági mentési intervallum és a megőrzés a Azure Cosmos DBban.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 0db34a615c9d92401e760c702feb0dbbf13ce01d
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803874"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Online biztonsági mentési és igény szerinti adatvisszaállítás Azure Cosmos DB

A Azure Cosmos DB rendszeres időközönként automatikusan biztonsági másolatot készít az adatairól. Az automatikus biztonsági mentések az adatbázis-műveletek teljesítményének vagy rendelkezésre állásának befolyásolása nélkül hozhatók. Az összes biztonsági mentés külön tárolódik a tárolási szolgáltatásokban, és ezek a biztonsági másolatok globálisan replikálódnak a regionális katasztrófák elleni rugalmasságra. Az automatikus biztonsági mentések olyan helyzetekben hasznosak, amikor véletlenül törli vagy frissíti az Azure Cosmos-fiókot,-adatbázist vagy-tárolót, és később szükség van az adatok helyreállítására.

## <a name="automatic-and-online-backups"></a>Automatikus és online biztonsági mentés

A Azure Cosmos DB, nem csupán az adatai, hanem az adatbiztonsági másolatok is nagyon redundánsak, és a regionális katasztrófák miatt rugalmasak. A következő lépések bemutatják, hogyan végzi el a Azure Cosmos DB az adatok biztonsági mentését:

* A Azure Cosmos DB 4 óránként automatikusan teljes biztonsági másolatot készít az adatbázisról, és a rendszer minden időpontban csak a legújabb két biztonsági mentést tárolja. Ha az alapértelmezett intervallumok nem elégségesek a számítási feladatokhoz, módosíthatja a biztonsági mentési időközt és a megőrzési időszakot a Azure Portal. A biztonsági mentési konfigurációt az Azure Cosmos-fiók létrehozásakor vagy azt követően is módosíthatja. Ha törli a tárolót vagy az adatbázist, a Azure Cosmos DB 30 napig megőrzi egy adott tároló vagy adatbázis meglévő pillanatképeit.

* Azure Cosmos DB tárolja ezeket a biztonsági másolatokat az Azure Blob Storage-ban, míg a tényleges adatok helyileg, Azure Cosmos DB belül találhatók.

* A kis késleltetés garantálása érdekében a biztonsági mentés pillanatképét az Azure Blob Storage tárolja ugyanabban a régióban, mint az aktuális írási régió (vagy az írási régiók **egyike** ), ha többrégiós írási konfigurációval rendelkezik. A regionális katasztrófákkal szembeni rugalmasság érdekében a rendszer az Azure Blob Storage-ban tárolt biztonsági mentési adatok minden pillanatképét újrareplikálja egy másik régióba a georedundáns tárolás (GRS) használatával. A biztonsági másolat replikálási régiója függ a forrásrégiótól és a forrásrégióhoz társított régiópártól. További információért tekintse meg a [geo-redundáns párok listáját az Azure-régiókról](../best-practices-availability-paired-regions.md) szóló cikkben. Ezt a biztonsági másolatot közvetlenül nem érheti el. Az Azure Cosmos DB csapatától támogatási kérésen keresztül igényelheti a biztonsági másolat visszaállítását.

   Az alábbi képen látható, hogy az USA nyugati régiójában lévő három elsődleges fizikai partícióval rendelkező Azure Cosmos-tároló hogyan készül biztonsági mentésben egy távoli Azure Blob Storage-fiókban az USA nyugati régiójában, majd replikálva az USA keleti régiójába:

  :::image type="content" source="./media/online-backup-and-restore/automatic-backup.png" alt-text="Az Azure Storage GRS összes Cosmos DB entitásának rendszeres teljes biztonsági mentése" border="false":::

* A biztonsági mentéseket az alkalmazás teljesítményének vagy rendelkezésre állásának befolyásolása nélkül kell elvégezni. Azure Cosmos DB végrehajtja az adatok biztonsági mentését a háttérben anélkül, hogy további kiosztott átviteli sebességet (RUs) kellene használnia, és nem befolyásolja az adatbázis teljesítményét és rendelkezésre állását.

## <a name="options-to-manage-your-own-backups"></a>A saját biztonsági mentések kezelésére szolgáló beállítások

Azure Cosmos DB SQL API-fiókokkal a következő módszerek egyikével is megőrizheti saját biztonsági mentését:

* A [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) használatával időnként áthelyezheti az adatátvitelt az Ön által választott tárhelyre.

* A teljes biztonsági mentéshez, illetve a növekményes változtatásokhoz és a saját tárolóban történő tárolásához használja a Azure Cosmos DB [változási hírcsatorna](change-feed.md) használatát az adat rendszeres olvasásához.

## <a name="modify-the-backup-interval-and-retention-period"></a>A biztonsági mentés intervallumának és megőrzési idejének módosítása

Azure Cosmos DB automatikusan teljes biztonsági másolatot készít az adatairól 4 óránként és bármikor, a legújabb két biztonsági mentést tárolja. Ez a konfiguráció az alapértelmezett beállítás, és további díj nélkül elérhető. Az alapértelmezett biztonsági mentési időköz és a megőrzési időszak az Azure Cosmos-fiók létrehozásakor vagy a fiók létrehozása után módosítható. A biztonsági mentési konfiguráció az Azure Cosmos-fiók szintjén van beállítva, és minden fióknál külön konfigurálnia kell. Miután konfigurálta egy fiók biztonsági mentési beállításait, a rendszer az adott fiókban lévő összes tárolóra alkalmazza. A biztonsági mentési beállításokat jelenleg csak az Azure Portalon változtathatja meg.

Ha véletlenül törölte vagy megsérült az adatai, az **adatok visszaállítására vonatkozó támogatási kérelem létrehozása előtt győződjön meg arról, hogy a fiók biztonsági mentése legalább hét napig megnövekszik. Az eseménytől számított 8 órán belül növelheti az adatmegőrzést.** Így az Azure Cosmos DB csapatának elég ideje lesz a fiók visszaállítására.

A következő lépésekkel módosíthatja egy meglévő Azure Cosmos-fiók alapértelmezett biztonsági mentési beállításait:

1. Jelentkezzen be a [Azure Portalba.](https://portal.azure.com/)
1. Navigáljon az Azure Cosmos-fiókjához, és nyissa meg a **biztonsági mentés & visszaállítás** panelt. Frissítse a biztonsági mentési időközt és a biztonsági mentés megőrzési időtartamát igény szerint.

   * **Biztonsági mentés időköze** – ez az az időtartam, amikor a Azure Cosmos db megkísérel biztonsági másolatot készíteni az adatairól. A biztonsági mentés nem nulla időt vesz igénybe, és bizonyos esetekben előfordulhat, hogy az alsóbb rétegbeli függőségek miatt sikertelen lesz. Azure Cosmos DB megpróbálja a legjobb biztonsági mentést készíteni a beállított időközönként, azonban nem garantálja, hogy a biztonsági mentés az adott időintervallumon belül befejeződik. Ezt az értéket órában vagy percben is konfigurálhatja. A biztonsági mentési időköz nem lehet kevesebb, mint 1 óra, és 24 óránál hosszabb. Ha megváltoztatja ezt az időközt, az új intervallum az utolsó biztonsági mentés időpontjától kezdve lép érvénybe.

   * **Biztonsági mentés megőrzése** – azt a pontot jelöli, amelyben az egyes biztonsági másolatok megmaradnak. Azt órákban vagy napokban is konfigurálhatja. A minimális megőrzési időtartam nem lehet kevesebb, mint a biztonsági mentési időköz (órában), és nem lehet nagyobb, mint 720 óra.

   * **Megőrzött Adatmásolatok** – alapértelmezés szerint a rendszer díjmentesen két biztonsági másolatot készít az adatairól. Ha kettőnél több példányra van szüksége, további díjat számítunk fel. A további másolatok pontos díjszabását az [árképzést ismertető oldal](https://azure.microsoft.com/pricing/details/cosmos-db/) Felhasznált tárterület szakaszában tekintheti meg.

   :::image type="content" source="./media/online-backup-and-restore/configure-backup-interval-retention.png" alt-text="Az Azure Storage GRS összes Cosmos DB entitásának rendszeres teljes biztonsági mentése" border="true":::

Ha a fiók létrehozása során konfigurálja a biztonsági mentési beállításokat, beállíthatja a **biztonsági mentési szabályzatot**, amely akár **rendszeres** , akár **folyamatos**. Az időszakos házirend lehetővé teszi a biztonsági mentés intervallumának és a biztonsági másolatok megőrzésének konfigurálását. A folyamatos házirend jelenleg csak a regisztráláskor érhető el. A Azure Cosmos DB csapat felméri a munkaterhelést, és jóváhagyja a kérést.

:::image type="content" source="./media/online-backup-and-restore/configure-periodic-continuous-backup-policy.png" alt-text="Az Azure Storage GRS összes Cosmos DB entitásának rendszeres teljes biztonsági mentése" border="true":::

## <a name="restore-data-from-an-online-backup"></a>Adatok visszaállítása online biztonsági mentésből

Az alábbi helyzetekben véletlenül törölheti vagy módosíthatja az adatait:  

* Törölje a teljes Azure Cosmos-fiókot.

* Töröljön egy vagy több Azure Cosmos-adatbázist.

* Töröljön egy vagy több Azure Cosmos-tárolót.

* Az Azure Cosmos-elemek (például dokumentumok) törlése vagy módosítása egy tárolón belül. Ezt a konkrét esetet általában adatsérülésnek nevezzük.

* A megosztott ajánlati adatbázisban lévő megosztott ajánlat-adatbázis vagy-tárolók törlődnek vagy sérültek.

A Azure Cosmos DB az összes fenti helyzetben képes visszaállítani az adatok visszaállítását. Visszaállításkor új Azure Cosmos-fiók jön létre a visszaállított adattároláshoz. Ha nincs megadva az új fiók neve, a formátuma lesz `<Azure_Cosmos_account_original_name>-restored1` . Az utolsó számjegy akkor nő, amikor több visszaállítást próbálnak meg. Egy előre létrehozott Azure Cosmos-fiókba nem állíthatók vissza az adathalmazok.

Ha véletlenül töröl egy Azure Cosmos-fiókot, visszaállíthatja az adathalmazt egy azonos nevű új fiókba, ha a fiók neve nincs használatban. Ezért azt javasoljuk, hogy a törlés után ne hozza újra létre a fiókot. Mivel ez nem csak azt akadályozza meg, hogy a visszaállított adatok ugyanazt a nevet használják, de a megfelelő fiókot is felhasználja a nehéz helyreállításhoz.

Ha véletlenül töröl egy Azure Cosmos-adatbázist, visszaállíthatja a teljes adatbázist vagy a tárolók egy részhalmazát az adatbázisban. Az adatbázisokban is kiválaszthat meghatározott tárolókat, és visszaállíthatja őket egy új Azure Cosmos-fiókba.

Ha véletlenül töröl vagy módosít egy vagy több elemet egy tárolón belül (az adatsérülési eset), meg kell adnia a visszaállításhoz szükséges időt. Az idő fontos, ha adatsérülés van. Mivel a tároló él, a biztonsági mentés továbbra is fut, így ha a megőrzési időszakon túl várakozik (az alapértelmezett érték nyolc óra), a rendszer felülírja a biztonsági mentéseket. **Ha meg szeretné akadályozni, hogy a biztonsági mentés felül legyen írva, növelje a fiók biztonsági mentését legalább hét napig. A legjobb, ha az adatsérüléstől számított 8 órán belül növelje az adatmegőrzést.**

Ha véletlenül törölte vagy megsérült az adatai, akkor 8 órán belül kapcsolatba kell lépnie az [Azure támogatási szolgálatával](https://azure.microsoft.com/support/options/) , hogy a Azure Cosmos db csapat segítséget nyújtson a biztonsági másolatokból származó adatok visszaállításához. Így a Azure Cosmos DB támogatási csapatnak elég ideje lesz a fiókja visszaállítására.

> [!NOTE]
> Az adat visszaállítása után a rendszer nem az összes forrás-képességet vagy beállítást a visszaállított fiókra hajtja végre. A következő beállítások nem kerülnek át az új fiókra:

> * VNET hozzáférés-vezérlési listája
> * Tárolt eljárások, eseményindítók és felhasználó által definiált függvények
> * Több régióra vonatkozó beállítások  

Ha az átviteli sebességet az adatbázis szintjén adja meg, a biztonsági mentési és visszaállítási folyamat ebben az esetben a teljes adatbázis szintjén történik, és nem az egyes tárolók szintjén. Ilyen esetekben nem választhatja ki a visszaállítani kívánt tárolók részhalmazát.

## <a name="migrate-data-to-the-original-account"></a>Az adatáttelepítés az eredeti fiókba

Az adatok visszaállításának elsődleges célja a véletlenül törölt vagy módosított adatok helyreállítása. Ezért javasoljuk, hogy először vizsgálja meg a helyreállított adatok tartalmát, hogy biztosan tartalmazza a várt adatokat. Később áttelepítheti az adatátvitelt az elsődleges fiókba. Habár lehetséges, hogy a visszaállított fiókot új aktív fiókként használja, ez nem ajánlott megoldás, ha éles számítási feladatokkal rendelkezik.  

Az alábbi módokon térhet vissza az eredeti Azure Cosmos-fiókba:

* Használja a [Azure Cosmos db adatáttelepítési eszközt](import-data.md).
* Használja a [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).
* Használja a [változási csatornát](change-feed.md) Azure Cosmos db.
* Írhat saját egyéni kódot is.

Győződjön meg arról, hogy az adatok migrálása után azonnal törli a visszaállított fiókokat, mivel ezek után folyamatos költségek merülnek fel.

## <a name="next-steps"></a>További lépések

A következő témakörből megtudhatja, hogyan állíthatja vissza az Azure Cosmos-fiók adatait, vagy megismerheti az adatok áttelepítését egy Azure Cosmos-fiókba

* A visszaállítási kérelem elvégzéséhez forduljon az Azure ügyfélszolgálatához, és kérjen [jegyet a Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Adatok visszaállítása Azure Cosmos-fiókból](how-to-backup-and-restore.md)
* Az adatAzure Cosmos DBba való áthelyezéshez [használja a Cosmos db módosítási csatornát](change-feed.md) .
* A [Azure Data Factory használatával](../data-factory/connector-azure-cosmos-db.md) viheti át az adatAzure Cosmos DBba.

