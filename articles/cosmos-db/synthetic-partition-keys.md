---
title: Szintetikus partíciókulcs létrehozása az Azure Cosmos DB-ben
description: Ismerje meg, hogyan használhatja a szintetikus partíciós kulcsokat az Azure Cosmos-tárolókban az adatok és a számítási feladatok egyenletes elosztásához a partíciós kulcsok között
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441222"
---
# <a name="create-a-synthetic-partition-key"></a>Szintetikus partíciókulcs létrehozása

Ajánlott eljárás, hogy egy partíciókulcs sok különböző értékeket, például több száz vagy több ezer. A cél az adatok és a számítási feladatok egyenletes elosztása a partíciókulcs-értékekhez társított elemek között. Ha ilyen tulajdonság nem létezik az adatokban, létrehozhat egy *szintetikus partíciókulcsot.* Ez a dokumentum számos alapvető technikát ismerteti a cosmos-tároló szintetikus partíciókulcsának létrehozásához.

## <a name="concatenate-multiple-properties-of-an-item"></a>Elem több tulajdonságának összefűzése

Partíciókulcsot úgy hozhat létre, hogy több tulajdonságértéket fűz `partitionKey` egyetlen mesterséges tulajdonságba. Ezeket a kulcsokat szintetikus kulcsoknak nevezzük. Vegyük például a következő példadokumentumot:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Az előző dokumentum esetében az egyik lehetőség a /deviceId vagy /date beállítása partíciókulcsként. Akkor használja ezt a beállítást, ha a tárolót az eszközazonosító vagy a dátum alapján szeretné particionálni. Egy másik lehetőség, hogy összefűzze `partitionKey` ezt a két értéket egy szintetikus tulajdonság, amely a partíciókulcsként használt.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Valós idejű esetekben több ezer elem lehet egy adatbázisban. Ahelyett, hogy a szintetikus kulcs manuálisan, ügyféloldali logika összefűzése értékek et, és helyezze be a szintetikus kulcsot az elemeket a Cosmos-tárolókban.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Partíciókulcs használata véletlenszerű utótaggal

Egy másik lehetséges stratégia a számítási feladatok egyenletesebb elosztásához egy véletlen szám hozzáfűzése a partíciókulcs-érték végén. Ha így osztja el az elemeket, párhuzamos írási műveleteket hajthat végre a partíciók között.

Erre példa, ha a partíciókulcs dátumot jelöl. Választhat egy 1 és 400 közötti véletlen számot, és összefűzheti a dátum utótagjaként. Ez a módszer partíciókulcs-értékeket eredményez, például `2018-08-09.1`a ,`2018-08-09.2`, és így tovább, a rendszeren keresztül. `2018-08-09.400` Mivel véletlenszerűen a partíciókulcsot, az írási műveletek a tároló minden nap egyenletesen oszlanak el több partíciót. Ez a módszer jobb párhuzamosságot és összességében nagyobb átviteli erőket eredményez.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Előre kiszámított utótagokkal rendelkező partíciókulcs használata 

A véletlenszerű utótag stratégia nagyban javíthatja az írási átviteli, de nehéz olvasni egy adott elemet. Nem ismeri az elem megírt eleméhez használt utótag értéket. Az egyes elemek könnyebb olvasása érdekében használja az előre kiszámított utótagok stratégiát. Ahelyett, hogy egy véletlen számot használna az elemek partíciók közötti elosztásához, használjon olyan számot, amelyet a lekérdezni kívánt elem alapján számít ki.

Tekintsük az előző példát, ahol egy tároló egy dátumot használ partíciókulcsként. Most tegyük fel, `Vehicle-Identification-Number` `VIN`hogy minden elem nek van egy ( ) attribútuma, amelyet el szeretnénk érni. Továbbá tegyük fel, hogy gyakran futtat `VIN`lekérdezéseket az elemek kereséséhez a dátum mellett. Mielőtt az alkalmazás írja az elemet a tárolóba, kiszámíthatja a kivonatutótagot a VIN alapján, és hozzáfűzheti a partíciókulcs dátumához. A számítás 1 és 400 közötti számot hozhat létre, amely egyenletesen oszlik el. Ez az eredmény hasonló a véletlenszerű utótag stratégiai módszer által elért eredményekhez. A partíciókulcs értéke ekkor a számított eredménnyel összefűzött dátum.

Ezzel a stratégiával az írási műveletek egyenletesen oszlanak el a partíciókulcs-értékek között és a partíciók között. Könnyen elolvashat egy adott elemet és dátumot, mert kiszámíthatja `Vehicle-Identification-Number`egy adott partíciókulcs értékét. Ennek a módszernek az az előnye, hogy elkerülheti egyetlen gyorspartíció-kulcs, azaz egy partíciókulcs létrehozását, amely az összes számítási feladatot igénybe veszi. 

## <a name="next-steps"></a>További lépések

A particionálási koncepcióról a következő cikkekben olvashat bővebben:

* További információ a [logikai partíciókról.](partition-data.md)
* További információ az [Azure Cosmos-tárolók és -adatbázisok átviteli átviteli módjáról.](set-throughput.md)
* Ismerje meg, hogyan [építheti ki az átviteli fazonát egy Azure Cosmos-tárolóban.](how-to-provision-container-throughput.md)
* Ismerje meg, hogyan [építheti ki az átviteli mertét egy Azure Cosmos-adatbázisban.](how-to-provision-database-throughput.md)
