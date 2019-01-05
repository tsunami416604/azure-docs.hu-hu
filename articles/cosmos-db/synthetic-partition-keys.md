---
title: Az Azure Cosmos DB szintetikus partíciókulcsok
description: Az Azure Cosmos DB-tárolók a szintetikus partíciókulcsok használata
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: mjbrown
ms.openlocfilehash: 37d220a13aec99de94afa3357db1462d11f8662c
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043368"
---
# <a name="create-a-synthetic-partition-key"></a>Szintetikus partíciós kulcs létrehozása

Akkor célszerű egy partíciókulcsot, például több száz vagy akár több ezer számos különböző értékekkel rendelkeznek. A cél, hogy az adatok és a számítási feladatok között ezek partíciós kulcsérték tartozó elemeket egyenletes elosztása. Ha ilyen tulajdonság nem létezik az adatokban, szintetikus partíciókulcs lehet létrehozni. A következő szakaszokban néhány alapvető technikából szintetikus partíciókulcsot a tároló létrehozásához.

## <a name="concatenating-multiple-properties-of-an-item"></a>Az elem több tulajdonságának kiszámításával

Elkülönített változó összefűzésével előállítjuk több tulajdonságértékek mesterséges egyetlen partíciókulcsot is útmutatását `partitionKey` tulajdonság. Ezek a kulcsok szintetikus kulcsok nevezzük. Vegyük példaként a következő példa dokumentumot:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Az előző dokumentumot az egyik lehetőség, hogy állítsa /deviceId vagy /date partíciókulcsként, ha a tároló vagy az eszköz azonosítója, vagy a dátum alapján szeretne. Egy másik lehetőség az, hogy ezen két karakterláncrészleteket be szintetikus `partitionKey` tulajdonságot partíciókulcsként használt.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

A valós idejű felhasználói helyzetek, rendelkezhet több ezer olyan dokumentumokat egy adatbázisban, ezért ahelyett, hogy manuálisan hozzáadja a szintetikus kulcs, be kell állítania az ügyféloldali logikát, fűz össze adatokat, és a dokumentumokat a szintetikus kulcs beszúrása.

## <a name="using-a-partition-key-with-random-suffix"></a>A partíciókulcsok használatával véletlenszerű utótaggal rendelkező

Egy másik lehetséges stratégia egyenletesebben okozott terhelés elosztásához, hogy hozzáfűzése egy véletlenszerű számot, a végén a partíciókulcs-értékkel. A módszer lehetővé teszi, hogy párhuzamos végrehajtása definíciófrissítés-terjesztés elemeinek írási műveletek partíciók között.

Például ha egy partíciókulcsot dátumot jelöli, előfordulhat, hogy válasszon egy 1 és 400 közé eső véletlenszerű szám és fűzze össze, Date utótagként. Ez a módszer eredményezi a partíciókulcs-értékek például a 2018-08-09.1, 2018-08-09.2, és így tovább, 2018-08-09.400 keresztül. A partíciókulcs vannak véletlenszerű elrendezés, mivel az írási műveletek az egyes napokon a tároló oszlik el egyenlően több partíciót. Ez a módszer a nagyobb párhuzamosság és átfogó nagyobb átviteli sebességet eredményez.

## <a name="using-a-partition-key-with-pre-calculated-suffixes"></a>A partíciós kulcs használata előre számított utótagok 

Véletlenszerű elrendezés stratégia nagy mértékben javíthatják a lemezírás teljesítménye, bár a nehezen olvasható egy cikket, mert nem tudja az utótag érték, amely a cikk írásakor. Könnyebben olvassa el az egyes elemeket, használhatja az előre számított utótagok stratégia. A partíciók között az elemeket, használja egy véletlenszerű számot használata helyett egy szám, amely kiszámítja valamit a lekérdezni kívánt alapján.

Fontolja meg az előző példában, amelyben egy tárolót használ a partíciókulcs a dátumot. Most tegyük fel, hogy minden elem elérhető VIN (jármű-azonosító-száma) attribútummal rendelkezik, és ha gyakran lekérdezéseket futtat szerint VIN, emellett található dátum. Az alkalmazás a cikk a tárolóba ír, mielőtt azt kiszámítása egy kivonatot utótagot a VIN alapján, és hozzáfűzi a partíciós kulcs dátum. A számítási 1 és 400 közötti szám, amely egyenlően elosztott, hasonlóan ahhoz, hogy az a véletlenszerű stratégia módszer által létrehozott eredményeket szolgáltathat. A partíciókulcs-értékkel lesz a dátum és a számított eredményeket.

Ezzel a stratégiával az írási műveletek egyenletesen oszlanak meg a partíciókulcs, és a partíciók között. Mivel a partíciókulcs-értékkel, kiszámíthatja az egy adott jármű-azonosító – száma egy adott elem és a dátum, egyszerűen olvasni. Ez a módszer az az előnye, hogy elkerülheti a gyakori elérésű egypartíciós kulcs (a partíciókulcsot, amely a számítási feladat) létrehozása. 

## <a name="next-steps"></a>További lépések

További tudnivalók a particionálási fogalma a következő cikkekben:

* Tudjon meg többet [logikai partíció](partition-data.md)
* Tudjon meg többet [kiépítési átviteli sebesség a Cosmos-tárolók és adatbázisok](set-throughput.md)
* Ismerje meg, [átviteli sebességet egy Cosmos-tárolón kiépítése](how-to-provision-container-throughput.md)
* Ismerje meg, [átviteli sebességet egy Cosmos-adatbázis kiépítése](how-to-provision-database-throughput.md)
