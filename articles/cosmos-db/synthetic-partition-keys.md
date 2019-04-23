---
title: Hozzon létre egy szintetikus partíciókulcsot az Azure Cosmos DB, az adatok és a számítási feladatok egyenletes elosztása.
description: Az Azure Cosmos-tárolókban szintetikus partíciókulcsok használata
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 6b3499c36ae7abd03c4a1f1ca3a3a46e2c315120
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792098"
---
# <a name="create-a-synthetic-partition-key"></a>Szintetikus partíciókulcs létrehozása

Az ajánlott eljárás egy partíciókulcsot, számos különböző értékekkel, például több száz vagy akár több ezer kell legyen. A cél, hogy az adatok és a számítási feladatok között ezek partíciós kulcsérték tartozó elemeket egyenletes elosztása. Ha ilyen tulajdonság nem létezik az az adatokat, hozhatnak létre egy *szintetikus partíciókulcs*. Ez a dokumentum azt ismerteti, néhány alapvető technikából szintetikus partíciókulcsot az Cosmos-tároló létrehozásához.

## <a name="concatenate-multiple-properties-of-an-item"></a>Az elem több tulajdonságának összefűzése

Elkülönített változó összefűzésével előállítjuk több tulajdonságértékek mesterséges egyetlen partíciókulcsot is útmutatását `partitionKey` tulajdonság. Ezek a kulcsok szintetikus kulcsok nevezzük. Vegyük példaként a következő példa dokumentumot:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Az előző dokumentumot az egyik lehetőség, hogy /deviceId vagy /date állítja be a partíciókulcs. Akkor használja ezt a beállítást, ha szeretné particionálni a tárolót, vagy az eszköz azonosítója, vagy a dátum alapján. Egy másik lehetőség az, hogy ezen két karakterláncrészleteket be szintetikus `partitionKey` tulajdonságot partíciókulcsként használt.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

A valós idejű felhasználói helyzetek ezer elem egy adatbázisban is. Helyett a szintetikus kulcs manuális hozzáadása, adja meg az ügyféloldali logikát, fűz össze adatokat, és a szintetikus kulcs beszúrása a cikkeket a Cosmos-tárolókban.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Olyan partíciókulcsot használni véletlenszerű utótaggal

Egy másik lehetséges stratégia egyenletesebben okozott terhelés elosztásához, hogy hozzáfűzése egy véletlenszerű számot, a végén a partíciókulcs-értékkel. Ezzel a módszerrel elemek terjesztésekor párhuzamos írási műveleteket végezhet a partíciók között.

Ilyen például, ha egy partíciókulcsot dátumot jelöli. Előfordulhat, hogy egy 1 és 400 közé eső véletlenszerű szám választja, és fűzze össze, Date utótagként. Ez a módszer eredményezi, például a partíciós kulcsérték `2018-08-09.1`,`2018-08-09.2`, és így tovább – `2018-08-09.400`. Ön véletlenszerűvé tétele partíciókulcsként, mert az írási műveletek az egyes napokon a tároló oszlik el egyenlően több partíciót. Ez a módszer a nagyobb párhuzamosság és átfogó nagyobb átviteli sebességet eredményez.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>A partíciós kulcs használata előre számított utótagok 

A véletlenszerű utótag stratégia nagy mértékben javíthatják a lemezírás teljesítménye, de nehezen olvasható egy adott elemet. Az elem megírt során használt utótag érték nem tudja. Könnyebben olvassa el az egyes elemeket, használja az előre számított utótagok stratégia. A partíciók között az elemeket, használja egy véletlenszerű számot használata helyett egy kiszámított számot valamit a lekérdezni kívánt alapján.

Fontolja meg az előző példában, ahol egy tárolót használja partíciókulcsként dátumot. Most tegyük fel, hogy rendelkezik-e minden elem egy `Vehicle-Identification-Number` (`VIN`) attribútum, amelyet meg szeretnénk elérni. További, tegyük fel, hogy milyen gyakran keresés szerinti elemek lekérdezéseket futtathat a `VIN`, dátum mellett. Az alkalmazás a cikk a tárolóba ír, mielőtt azt kiszámítása egy kivonatot utótagot a VIN alapján, és hozzáfűzi a partíciós kulcs dátum. A számítás generálhat, 1 és 400 közötti szám, amely egyenletesen vannak elosztva. Ez az eredmény az eredményeket a véletlenszerű utótag stratégia metódus által előállított hasonlít. A partíciókulcs értékét az majd a dátum és a számított eredményeket.

Ezzel a stratégiával az írási műveletek egyenletesen oszlanak meg a partíciókulcs, és a partíciók között. Könnyedén talál egy adott elem és a dátum, mivel a partíciós kulcs értékét egy adott kiszámíthatja `Vehicle-Identification-Number`. Ez a módszer előnye, hogy akkor is ne hozzon létre egy egyetlen gyakori elérésű partíciókulcsot, azaz a partíciókulcsot, amely a számítási feladatok. 

## <a name="next-steps"></a>További lépések

További tudnivalók a particionálási fogalma a következő cikkekben:

* Tudjon meg többet [logikai partíció](partition-data.md).
* Ismerje meg, hogyan [az Azure Cosmos-tárolók és adatbázisok kiépítése átviteli](set-throughput.md).
* Ismerje meg, hogyan [üzembe helyezése egy Azure Cosmos-tároló átviteli sebességet](how-to-provision-container-throughput.md).
* Ismerje meg, hogyan [üzembe helyezése egy Azure Cosmos database átviteli sebességet](how-to-provision-database-throughput.md).
