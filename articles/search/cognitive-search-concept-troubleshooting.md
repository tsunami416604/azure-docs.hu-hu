---
title: Az Azure Search kognitív keresési tippek |} Microsoft Docs
description: Tippek és kognitív beállításával kapcsolatos hibaelhárítás keresési folyamatok az Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 3c3f9a0d0dc40de6c62c21dab0f11a501829ef11
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640965"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Hibaelhárítási kognitív keresési tippek

Ez a cikk tippek és kognitív keresési képességek az Azure Search használatába, áthelyezés során trükkök listáját tartalmazza. 

Ha még nem tette meg, Lépkedjen végig a [oktatóanyag: megtudhatja, hogyan hívhatja meg a kognitív keresési API-k](cognitive-search-quickstart-blob.md) az eljárás alkalmazása kognitív keresési fokozatokká blob adatforráshoz.

## <a name="tip-1-start-with-a-small-dataset"></a>1. tipp: Kezdje kisebb adatkészlet
A legjobb módszer a problémák gyorsan előkeresheti a sebesség, amellyel kapcsolatos problémák megoldása növelése érdekében. Indexelési idő csökkentéséhez legkönnyebben indexelése dokumentumok számának csökkentésével. 

Először hozzon létre egy adatforrást a csupán néhány olyan dokumentumok/rekordok. A dokumentum minta a helyes megjelenítése a különböző azt jelzi, hogy lesz indexelve kell lennie. 

A dokumentum minta futtatásához a végpont feldolgozási folyamaton keresztül, és ellenőrizze, hogy az eredmények az igényeinek. Ha elégedett az eredményeket, adhat hozzá további fájlok az adatokat az adatforrásból.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>2. tipp: Ellenőrizze, hogy az adatforrás hitelesítő adatai helyesek.
Az adatforrás-kapcsolat nincs érvényesítve, amíg nem adhat meg az indexelő, ami azt használja. Ha jelenik meg, hogy az indexelő tudja elérni az adatok megemlíteni hibáit, győződjön meg arról, hogy:
- A kapcsolati karakterlánc helyességéről. Kifejezetten SAS-tokenje hoz létre, amikor győződjön meg arról, hogy az Azure Search által várt formátum használata. Lásd: [megadása hitelesítő adatai szakaszban](
https://docs.microsoft.com/en-us/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) további információt a különböző formátumokban támogatja.
- A tároló neve az indexelőben helyességéről.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>3 tipp: Mi működik, ha az egyes hibák lásd
Néha kis hiba leállítja a nyomon követi az indexelőt. Ez ideális, ha azt tervezi, egyenként problémáinak megoldásával kapcsolatban. Azonban érdemes figyelmen kívül hagyja a hibát, egy adott típusú így folytatja, hogy mi adatfolyamok ténylegesen dolgozik látható az indexelő.

Ebben az esetben érdemes állapítható meg, hogy az indexelő figyelmen kívül hagyja a hibát. Teheti meg, hogy *maxFailedItems* és *maxFailedItemsPerBatch* – 1 az indexelő definíciójának részeként.

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>4 tipp: A technikai részletek bővített dokumentumok megnézi 
Bővített dokumentumok olyan ideiglenes struktúrák dúsító során létrehozott, és a törölt Ha feldolgozása befejeződött.

Ha pillanatképet szeretne készíteni az indexelés során létrejött bővített dokumentumról, adja hozzá az indexhez az ```enriched``` mezőt. Az indexelő automatikusan hozzáadja a mezőhöz az adott dokumentum bővítéseinek karakterláncos leképezését.

Az ```enriched``` mező egy sztringet tartalmaz, amely a JSON-ban szereplő memóriabeli bővített dokumentum logikai leképezése.  A mező értéke azonban egy érvényes JSON-dokumentum. Mivel az idézőjelek előtt escape-karakter áll, a `\"` karaktereket `"` karakterre kell cserélnie, ha a dokumentumot formázott JSON-ként szeretné megtekinteni. 

A bővített mező szánt hibakeresési célra csak, megismerheti a tartalom kifejezések elleni alatt kiértékelt logikai alakját. Ön nem ebben a mezőben az indexelés céljából függ.

Adja hozzá egy ```enriched``` mező az index definícióját hibakeresési célra részeként:

#### <a name="request-body-syntax"></a>Kéréstörzs szintaxisa
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-5-expected-content-fails-to-appear"></a>5. tipp: Várt tartalom nem jelenik meg

Hiányzó tartalomra lehet az eredménye a dokumentumok indexelés során megszakad. Szabad és alapvető rétegek a dokumentum mérete alacsony korlátokkal rendelkeznek. Minden olyan fájlt, a túllépő indexelés során megszakad. Az Azure portálon eldobott dokumentumok ellenőrizheti. A search szolgáltatás irányítópultján kattintson duplán az indexelők csempe. Tekintse át a sikeres indexelt dokumentumok aránya. Ha nem 100 %-os, kattintson a további információkhoz juthat a beolvasandó aránya. 

Ha a probléma fájlméret kapcsolódik, akkor merülhetnek fel ilyen hiba: "a < fájlnév >" blobjához < Fájl-> /mp, a méretet, amelyet meghaladja a maximális méretet, a jelenlegi szolgáltatási szinthez dokumentum kiolvasásához. " Az indexelő korlátozások további információkért lásd: [szolgáltatási korlátait](search-limits-quotas-capacity.md).

Előfordulhat, hogy a tartalom nem jelenik meg a második okát kapcsolódó bemeneti/kimeneti hozzárendelési hibák. Például egy kimeneti cél neve "Személyek" viszont az index mező neve kisbetűs "személyek". A rendszer sikerült 201 sikeres üzeneteket visszaküldeni a teljes adatcsatorna úgy gondolja, hogy indexelő sikeres, ha ténylegesen mező üres. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>6. tipp: Kiterjesztése feldolgozása meghaladja a maximálisan engedélyezett futási időt (24 órás időszak)

Kép elemzésre számításilag-igényes egyszerű esetben, ha képek különösen nagy vagy összetett, feldolgozási időt lépheti túl megengedett maximális időtartamot. 

Maximálisan engedélyezett futási idő függ a réteg: több percet a szabad a réteg, 24 órás számlázható rétegen indexelő. Ha feldolgozási egy 24 órás időszakban igény szerinti feldolgozás befejezése, váltson egy ütemezését, úgy, hogy az indexelő feldolgozási átvételéhez, ahol abbamaradtak. 

Az ütemezett indexelők indexelő ütemezés szerint, a legutóbbi ismert helyes dokumentum folytatása. Ismétlődő ütemezés szerint használatával az indexelő is működnek a keresztül a kép várakozó több óráig vagy napig, minden replika nélküli módosításról feldolgozott lemezképek feldolgozásáig. Az ütemezés szintaxisa további információkért lásd: [3. lépés: hozzon létre-az-indexelő](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer).

Portál-alapú az indexelés (leírtak a gyors üzembe helyezés), válassza az "egyszeri futtatás" indexelő lehetőséget 1 óra feldolgozási korlátok (`"maxRunTime": "PT1H"`). Előfordulhat, hogy szeretnék kibővíteni a egy hosszabb feldolgozási időszakában.

## <a name="tip-7-increase-indexing-throughput"></a>7. tipp: Növelje az indexelési teljesítmény

A [párhuzamos indexelő](search-howto-large-index.md), helyezze az adatokat több tároló vagy ugyanabban a tárolóban található több virtuális mappákat. Ezután hozzon létre több datasource és indexelő párokat. Összes indexelő használhatja az azonos skillset és írni az azonos cél-keresési index, ezért a fájlkeresés alkalmazás nem kell figyelembe vennie a particionálást.
További információkért lásd: [nagy adatkészletek indexelő](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Lásd még
+ [Gyors üzembe helyezés: Kognitív keresési folyamat létrehozása a portálon](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: További kognitív search REST API-k](cognitive-search-tutorial-blob.md)
+ [Az adatforrás hitelesítő adatainak megadása](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Az indexelő nagy adatkészletek](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
+ [Hogyan képezheti bővített mezők index](cognitive-search-output-field-mapping.md)