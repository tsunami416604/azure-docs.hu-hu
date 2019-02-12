---
title: Hozzon létre egy szintetikus partíciókulcsot az Azure Cosmos DB, az adatok és a számítási feladatok egyenletes elosztása.
description: Az Azure Cosmos-tárolókban szintetikus partíciókulcsok használata
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: mjbrown
ms.openlocfilehash: 8becfe375f2e887348729cf1d76820fc41156d2a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997100"
---
# <a name="create-a-synthetic-partition-key"></a>Szintetikus partíciókulcs létrehozása

Ajánlott eljárás az, számos különböző értékekkel, például több száz vagy akár több ezer olyan partíciókulcsot kell legyen. A cél, hogy az adatok és a számítási feladatok között ezek partíciós kulcsérték tartozó elemeket egyenletes elosztása. Ha ilyen tulajdonság nem létezik az adatokban, összeállíthatja a szintetikus partíciókulcsot. A következő szakaszokban néhány alapvető technikából szintetikus partíciókulcsot a tároló létrehozásához.

## <a name="concatenate-multiple-properties-of-an-item"></a>Az elem több tulajdonságának összefűzése

Elkülönített változó összefűzésével előállítjuk több tulajdonságértékek mesterséges egyetlen partíciókulcsot is útmutatását `partitionKey` tulajdonság. Ezek a kulcsok szintetikus kulcsok nevezzük. Vegyük példaként a következő példa dokumentumot:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Az előző dokumentumot az egyik lehetőség, hogy /deviceId vagy /date állítja be a partíciókulcs. Használja ezt a beállítást, ha szeretné particionálni a tárolót, vagy az eszköz azonosítója, vagy a dátum alapján. Egy másik lehetőség az, hogy ezen két karakterláncrészleteket be szintetikus `partitionKey` tulajdonságot partíciókulcsként használt.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

A valós idejű felhasználói helyzetek rendelkezhet több ezer adatbázis dokumentumok. Helyett a szintetikus kulcs manuális hozzáadása, adja meg az ügyféloldali logikát, fűz össze adatokat, és a szintetikus kulcs beszúrása a dokumentumokat.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Olyan partíciókulcsot használni véletlenszerű utótaggal

Egy másik lehetséges stratégia egyenletesebben okozott terhelés elosztásához, hogy hozzáfűzése egy véletlenszerű számot, a végén a partíciókulcs-értékkel. Ezzel a módszerrel elemek terjesztésekor párhuzamos írási műveleteket végezhet a partíciók között.

Ilyen például, ha egy partíciókulcsot dátumot jelöli. Előfordulhat, hogy egy 1 és 400 közé eső véletlenszerű szám választja, és fűzze össze, Date utótagként. Ez a módszer eredményezi a partíciókulcs-értékek például a 2018-08-09.1, 2018-08-09.2, és így tovább, 2018-08-09.400 keresztül. Ön véletlenszerűvé tétele partíciókulcsként, mert az írási műveletek az egyes napokon a tároló oszlik el egyenlően több partíciót. Ez a módszer a nagyobb párhuzamosság és átfogó nagyobb átviteli sebességet eredményez.

## <a name="use-a-partition-key-with-precalculated-suffixes"></a>A partíciós kulcs használata előre kiszámított utótagok 

A randomizing stratégia nagy mértékben javíthatják a lemezírás teljesítménye, de nehezen olvasható egy adott elemet. Nem ismeri az utótag érték, amely a cikk írásakor. Könnyebben olvassa el az egyes elemeket, használja az előre kiszámított utótagok stratégia. A partíciók között az elemeket, használja egy véletlenszerű számot használata helyett egy szám, amely kiszámítja valamit a lekérdezni kívánt alapján.

Fontolja meg az előző példában, amelyben egy tárolót használ a partíciókulcs a dátumot. Most tegyük fel, hogy minden elem elérhető a jármű-azonosító-száma (VIN) attribútummal rendelkezik. További tegyük fel, hogy milyen gyakran mellett dátum szerint VIN, elemek keresése lekérdezéseket futtathat. Az alkalmazás a cikk a tárolóba ír, mielőtt azt kiszámítása egy kivonatot utótagot a VIN alapján, és hozzáfűzi a partíciós kulcs dátum. A számítás generálhat, 1 és 400 közötti szám, amely egyenletesen vannak elosztva. Ez az eredmény az eredményeket a véletlenszerű stratégia metódus által előállított hasonlít. A partíciókulcs értékét az majd a dátum és a számított eredményeket.

Ezzel a stratégiával az írási műveletek egyenletesen oszlanak meg a partíciókulcs, és a partíciók között. Mert, kiszámíthatja a partíciókulcs értékét egy adott jármű-azonosító – maximális száma egy adott elem és a dátum könnyen olvasható. Ez a módszer az az előnye, hogy elkerülheti a gyakori elérésű egypartíciós kulcs létrehozása. Gyakori elérésű partíciókulcsot a partíciós kulcs, amely a számítási feladat. 

## <a name="next-steps"></a>További lépések

További tudnivalók a particionálási fogalma a következő cikkekben:

* Tudjon meg többet [logikai partíció](partition-data.md).
* Ismerje meg, hogyan [az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli](set-throughput.md).
* Ismerje meg, hogyan [üzembe helyezése egy Azure Cosmos-tároló átviteli sebességet](how-to-provision-container-throughput.md).
* Ismerje meg, hogyan [üzembe helyezése egy Azure Cosmos database átviteli sebességet](how-to-provision-database-throughput.md).
