---
title: Az automatikus, online biztonsági mentés és igény szerinti adatok visszaállítása az Azure Cosmos DB-ben
description: Ez a cikk azt ismerteti, hogyan automatikus, online biztonsági mentés és igény szerinti adatok visszaállítása az Azure Cosmos DB működik.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 6ed968b1613a96a2f4ab449c7b52488e066a38ab
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991818"
---
# <a name="online-backup-and-on-demand-data-restore-in-azure-cosmos-db"></a>Online biztonsági mentés és igény szerinti adatok visszaállítása az Azure Cosmos DB-ben

Az Azure Cosmos DB az adatok biztonsági másolatait a rendszeres időközönként automatikusan vesz igénybe. Az automatikus biztonsági mentést készít a teljesítmény vagy az adatbázis-művelet rendelkezésre állásának befolyásolása nélkül. A biztonsági mentések külön-külön tárolja, egy tárolási szolgáltatásban, és ezeket a biztonsági mentéseket globálisan replikálva vannak a regionális katasztrófa szembeni ellenálló-képesség. Az automatikus biztonsági mentést akkor esetekben hasznos, ha véletlenül töröl, vagy frissítse az Azure Cosmos-fiók, adatbázis vagy tároló és az újabb megkövetelése az adat-helyreállítás.

## <a name="automatic-and-online-backups"></a>Az automatikus és online biztonsági mentések tiltása

Az Azure Cosmos DB nem csak az adatokat, de az adatok biztonsági mentését is a magas redundáns és rugalmas regionális katasztrófák. A következő lépések bemutatják, hogyan hajtja végre az Azure Cosmos DB az adatok biztonsági mentése:

* Az Azure Cosmos DB automatikusan fogadja az adatbázis biztonsági másolatának 4 óránként, és bármikor idő, csak a legújabb 2 biztonsági másolatai. Azonban, ha a tároló vagy az adatbázis törölve van, az Azure Cosmos DB egy adott tároló adatbázis vagy a meglévő pillanatképeket 30 napig őrzi meg.

* Az Azure Cosmos DB tárolja ezeket a biztonsági másolatokat az Azure Blob storage-ban, míg a tényleges adatok helyben Azure Cosmos DB.

*  Garantálja az alacsony késés, az a biztonsági mentési pillanatképet az Azure Blob storage-ban és az aktuális írási régióba ugyanabban a régióban tárolja (vagy egy írási régiót, abban az esetben, ha több főkiszolgálós konfigurációt) az Azure Cosmos-adatbázis-fiók. A regionális katasztrófa elleni rugalmasság érdekében minden pillanatképet a biztonsági mentési adatok Azure Blob storage-ban újra replikálása a georedundáns tárolás (GRS) egy másik régióban. A régió, amelyre a biztonsági mentés a rendszer replikálja a forrásrégióban és a regionális párokból érdemes a forrásrégióban társított alapul. További tudnivalókért tekintse meg a [Azure-régióra, georedundáns értékpárok listáját](../best-practices-availability-paired-regions.md) cikk. Ez a biztonsági másolat nem érhetők el közvetlenül. Az Azure Cosmos DB fogja használni a biztonsági mentés, csak akkor, ha egy biztonsági mentési visszaállítás kezdeményezhető.

* A biztonsági mentések teljesítményére vagy az alkalmazás rendelkezésre állásának befolyásolása nélkül kerül. Az Azure Cosmos DB hajt végre, minden további kiosztott átviteli kapacitás (RU) használó vagy a teljesítmény és az adatbázis rendelkezésre állásának befolyásolása nélkül az adatok biztonsági mentése a háttérben.

* Ha véletlenül törölt vagy az adatok sérültek, forduljon [az Azure-támogatás](https://azure.microsoft.com/support/options/) 8 órán belül, hogy az Azure Cosmos DB-csapatunk segíthet helyreállítja az adatokat a biztonsági mentésekből.

A következő kép bemutatja, hogyan egy Azure Cosmos-tárolóhoz, az összes a három elsődleges fizikai partíciók az USA nyugati RÉGIÓJA az USA nyugati Régiójában egy távoli Azure Blob Storage-fiók biztonsági mentésének és replikálja az USA keleti régiója:

![Rendszeres teljes biztonsági mentést az összes Cosmos DB-entitások GRS Azure Storage-ban](./media/online-backup-and-restore/automatic-backup.png)

## <a name="options-to-manage-your-own-backups"></a>Lehetőség a saját biztonsági másolatainak kezelése

A Azure Cosmos DB SQL API-fiókok esetében is létrehozhat saját biztonsági másolatokat készítenie a következő megközelítések egyikének használatával:

* Használat [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) adatok rendszeres időközönként áthelyezése egy tetszőleges Storage.

* Az Azure Cosmos dB-ben [módosításcsatornáját](change-feed.md) adatokat rendszeres időközönként olvassa a teljes biztonsági mentéseket, valamint a növekményes változásokat és az tárolóban tárolja.

## <a name="backup-retention-period"></a>Biztonsági másolat megőrzési idejének

Az Azure Cosmos DB az adatok pillanatképeket készít négy óránként. Egy adott időpontban csak az utolsó két pillanatfelvételek megmaradnak. Azonban, ha a tároló vagy az adatbázis törölve van, az Azure Cosmos DB egy adott tároló adatbázis vagy a meglévő pillanatképeket 30 napig őrzi meg.

## <a name="restoring-data-from-online-backups"></a>Adatok online biztonsági másolatokból való visszaállítással

Véletlen törlés és az adatok módosítását az alábbi esetek egyikében fordulhat elő:  

* A teljes Azure-Cosmos-fiókot törölték

* Egy vagy több Azure-Cosmos-adatbázis törlése

* Egy vagy több Azure-Cosmos-tárolók törlése

* Az Azure Cosmos elemek (például dokumentumok) egy tárolóból vannak törölték vagy módosították. Ebben az esetben általában "adatsérülés" néven.

* Egy megosztott ajánlat adatbázis vagy egy megosztott ajánlat adatbázison belül tárolók törlése vagy sérült állapotba kerül

Az Azure Cosmos DB is helyreállíthatja az adatokat a fenti forgatókönyvekben. A visszaállítási folyamat mindig létrehoz egy új Azure Cosmos-fiókot a visszaállított adatok tárolásához. Az új fiók neve nincs megadva, ha lesz a formátum `<Azure_Cosmos_account_original_name>-restored1`. Az utolsó számjegy értéke akkor növekszik, visszaállítás több próbálkozás is. Egy előre létrehozott Azure Cosmos-fiók nem állítható vissza adatokat.

Egy Azure Cosmos-fiók törlése esetén azt állíthatja vissza az adatok egy fiókba, ezzel a névvel, feltéve, hogy a fiók neve nem szerepel a használatát. Ezekben az esetekben javasoljuk, hogy nem hozza létre újra a fiókot törlés után mert nemcsak megakadályozza, hogy ugyanazt a nevet a visszaállított adatok, de révén felderítése nehezebb visszaállítása a megfelelő fiókot is. 

Egy Azure Cosmos-adatbázis törlése esetén visszaállíthatja a teljes adatbázis vagy a tárolókra, hogy az adatbázis egy részét. Egyben lehetővé teszi a tárolók kiválasztása adatbázis között, és visszaállíthatja őket, és a visszaállított adatok el van helyezve, az új Azure Cosmos-fiókhoz az összes.

Egy vagy több elemet egy tárolón belül véletlenül törölt vagy módosított (az adatok sérülése eset), meg kell adnia történő helyreállításához szükséges időt. Ebben az esetben a legfontosabb jellemzője, ideje. Mivel a tároló élő, a biztonsági mentés még fut, így ha a megőrzési időszak (az alapértelmezett érték nyolc óra) után vár felülírják-e a biztonsági másolatokat. Esetén törölhet az adatok már nem tárolódik, mivel azok nem felül a biztonsági mentési ciklust. A törölt adatbázisok vagy tárolók másolatok 30 napig.

Ha Ön oszthatnak ki átviteli kapacitásokat az adatbázis szintjén (azt jelenti, ahol tárolók készletét megosztja a kiosztott átviteli sebesség), a biztonsági mentési és helyreállítási folyamatokat ebben az esetben fordulhat elő, a teljes adatbázis szintjén, és nem az egyes tárolók szintjén. Ezekben az esetekben állítsa vissza a tárolókban kiválasztásával lehetőség nem.

## <a name="migrating-data-to-the-original-account"></a>Adatok áttelepítése az eredeti fiók

Az adatok visszaállítása elsődleges célja, hogy törli vagy véletlenül módosítása adatok helyreállítását módon. Ezért azt javasoljuk, először győződjön meg arról, mit sebességhez tartalmaz, a helyreállított adatok tartalmának vizsgálata. Ezután térjen vissza az elsődleges fióknak az adatok áttelepítése működik. Bár a visszaállított fiókot használja, mint a Microsoft-fiók, akkor sem ajánlott beállítás ha termelési számítási feladatokhoz.  

Vissza az eredeti Azure-Cosmos-fiók migrálni az adatokat különböző módjai a következők:

* Használatával [Cosmos DB az adatok áttelepítési eszköz](import-data.md)
* Használatával [az Azure Data Factory]( ../data-factory/connector-azure-cosmos-db.md)
* Használatával [módosításcsatornáját](change-feed.md) Azure Cosmos DB-ben 
* Egyéni kód írása

Törölje a visszaállított fiókok, amint elkészült migrálása, mert azok fog további költségekkel.

## <a name="next-steps"></a>További lépések

Ezután megismerheti a adatainak visszaállítása egy Azure Cosmos-fiók, és ismerje meg, hogyan telepíthet át adatokat egy Azure Cosmos-fiókhoz

* Győződjön meg arról, a visszaállítási kérelem, az Azure ügyfélszolgálatától a [jegyet az Azure Portalról](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
* [Adatok visszaállítása egy Azure Cosmos-fiók](how-to-backup-and-restore.md)
* [Cosmos DB használatát a módosítási hírcsatornáról](change-feed.md) adatok áthelyezése az Azure Cosmos DB-hez.
* [Azure Data Factory használata](../data-factory/connector-azure-cosmos-db.md) adatok áthelyezése az Azure Cosmos DB-hez.

