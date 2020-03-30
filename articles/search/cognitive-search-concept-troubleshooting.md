---
title: Tippek a ai-dúsítás tervezéséhez
titleSuffix: Azure Cognitive Search
description: Tippek és hibaelhárítás az AI-bővítési folyamatok azure Cognitive Search beállításához.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3fef5db90c3ae63a8fa48835646e09f9dfe6f023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245485"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Tippek a a i-k gazdagításhoz az Azure Cognitive Search szolgáltatásban

Ez a cikk az Azure Cognitive Search ai-bővítési képességeinek első lépéseit követően mozgásban tartandó tippek és trükkök listáját tartalmazza. 

Ha még nem tette meg, lépjen végig az [oktatóanyagon: Ismerje meg, hogyan hívhatja meg az AI-bővítési API-kat](cognitive-search-quickstart-blob.md) az AI-dúsítások blob adatforrásra történő alkalmazásának gyakorlatához.

## <a name="tip-1-start-with-a-small-dataset"></a>1. tipp: Kezdés kis adatkészlettel
A problémák gyors megtalálásának legjobb módja a problémák megoldásának sebessége. Az indexelési idő csökkentésének legjobb módja az indexelendő dokumentumok számának csökkentése. 

Először hozzon létre egy adatforrást csak néhány dokumentummal/rekorddal. A dokumentummintának jól kell ábrázolnia az indexelt dokumentumok sokféleségét. 

Futtassa a dokumentummintát a végpontok között futó folyamaton keresztül, és ellenőrizze, hogy az eredmények megfelelnek-e az igényeinek. Ha elégedett az eredménnyel, további fájlokat adhat az adatforráshoz.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>2. tipp: Ellenőrizze, hogy az adatforrás hitelesítő adatai helyesek-e
Az adatforrás-kapcsolat nem érvényesítve, amíg meg nem határoz egy indexelőt, amely azt használja. Ha bármilyen hibát észlel, amely szerint az indexelő nem tudja megkeresni az adatokat, győződjön meg arról, hogy:
- A kapcsolati karakterlánc helyes. Különösen sas-jogkivonatok létrehozásakor győződjön meg arról, hogy az Azure Cognitive Search által várt formátumot használja. A különböző támogatott formátumokról a [Hitelesítő adatok megadása című témakörben](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) olvashat.
- A tároló neve az indexelőben helyes.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>3. tipp: Nézze meg, mi működik, még akkor is, ha vannak hibák
Néha egy kis hiba megállítja az indexelőt a sávban. Ez rendben van, ha azt tervezi, hogy erősít kérdések egyenként. Előfordulhat azonban, hogy figyelmen kívül szeretne hagyni egy adott típusú hibát, lehetővé téve az indexelő folytatását, hogy láthassa, hogy a folyamatok valójában hogyan működnek.

Ebben az esetben érdemes lehet megmondani az indexelőnek, hogy hagyja figyelmen kívül a hibákat. Ehhez a *maxFailedItems* és *a maxFailedItemsPerBatch* értéket -1-ként kell beállítania az indexelő definíciója részeként.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>4. tipp: A motorháztető alatti bővített dokumentumok 
A bővített dokumentumok a dúsítás során létrehozott ideiglenes struktúrák, majd a feldolgozás befejezésekor törlődnek.

Ha pillanatképet szeretne készíteni az indexelés során létrejött bővített dokumentumról, adja hozzá az indexhez az ```enriched``` mezőt. Az indexelő automatikusan hozzáadja a mezőhöz az adott dokumentum bővítéseinek sztringes leképezését.

Az ```enriched``` mező egy sztringet tartalmaz, amely a JSON-ban szereplő memóriabeli bővített dokumentum logikai leképezése.  A mező értéke azonban egy érvényes JSON-dokumentum. Mivel az idézőjelek előtt escape-karakter áll, a `\"` karaktereket `"` karakterre kell cserélnie, ha a dokumentumot formázott JSON-ként szeretné megtekinteni. 

A bővített mező csak hibakeresési célokra szolgál, hogy segítsen megérteni annak a tartalomnak a logikai alakját, amelyellen a kifejezések kiértékelése folyamatban van. Indexelési célokból nem függhet ettől a mezőtől.

```enriched``` Mező hozzáadása az indexdefiníció részeként hibakeresés céljából:

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

## <a name="tip-5-expected-content-fails-to-appear"></a>5. tipp: A várt tartalom nem jelenik meg

A hiányzó tartalom az indexelés során a dokumentumok eldobásának eredménye lehet. Az ingyenes és az alapszintű csomagok alacsony korláttal rendelkeznek a dokumentumméretre vonatkozóan. A korlátot meghaladó fájlok az indexelés során eldobódnak. Az eldobott dokumentumok az Azure Portalon. A keresési szolgáltatás irányítópultján kattintson duplán az Indexelők csempére. Tekintse át az indexelt sikeres dokumentumok arányát. Ha nem 100%, akkor kattintson az arány, hogy minél több részletet. 

Ha a probléma a fájlmérettel kapcsolatos, a következőhöz \<hasonló hibaüzenet jelenhet meg: \<"A blob fájlnév>" fájlméret-> bájtméretű, ami meghaladja az aktuális szolgáltatási szint dokumentumkinyerésének maximális méretét." Az indexelőkorlátokról a [Szolgáltatáskorlátok](search-limits-quotas-capacity.md)című témakörben talál további információt.

A tartalom megjelenésének másik oka lehet a kapcsolódó bemeneti/kimeneti leképezési hiba. A kimeneti célneve például "Személyek", de az indexmező neve kisbetűs "személyek". A rendszer 201 sikeres üzenetet adhat vissza a teljes folyamathoz, így úgy gondolja, hogy az indexelés sikeres volt, amikor egy mező valójában üres. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>6. tipp: A feldolgozás kiterjesztése a maximális futási időn túl (24 órás ablak)

A képelemzés még egyszerű esetekben is számításigényes, így ha a képek különösen nagyok vagy összetettek, a feldolgozási idő meghaladhatja a maximálisan engedélyezett időt. 

A maximális futási idő rétegenként változik: néhány perc az ingyenes szinten, 24 órás indexelés a számlázható szinteken. Ha a feldolgozás nem fejeződik be egy 24 órás időszakon belül az igény szerinti feldolgozás, váltson át egy ütemezést, hogy az indexelő vegye fel a feldolgozást, ahol abbahagyta. 

Ütemezett indexelők esetén az indexelés az ütemezés szerint folytatódik az utolsó ismert helyes dokumentumnál. Az ismétlődő ütemezés használatával az indexelő órák vagy napok sorozatán keresztül dolgozhat végig a képhátralékon, amíg az összes fel nem dolgozott kép feldolgozása meg nem halad. Az ütemezés szintaxisáról további információt a [3.](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) [How to schedule indexers for Azure Cognitive Search](search-howto-schedule-indexers.md)

> [!NOTE]
> Ha egy indexelő egy bizonyos ütemezésre van beállítva, de minden egyes futtatáskor ismételten sikertelen ugyanazon a dokumentumon, az indexelő kevésbé gyakori időközönként (legfeljebb 24 óránként egyszer) kezd futni, amíg sikeresen nem halad előre. Újra.  Ha úgy gondolja, hogy kijavította azt a problémát, amely miatt az indexelő egy bizonyos ponton elakadt, végrehajthatja az indexelő igény szerinti futtatását, és ha ez sikeresen halad előre, az indexelő ismét visszatér a beállított ütemezési időközéhez.

Portálalapú indexelés esetén (a rövid útmutatóban leírtak szerint) az "egyszer futtatás" indexelő beállítás kiválasztása 1 órára korlátozza a feldolgozást.`"maxRunTime": "PT1H"` Érdemes lehet kiterjeszteni a feldolgozási ablakot valami hosszabbra.

## <a name="tip-7-increase-indexing-throughput"></a>7. tipp: Az indexelési átviteli arány növelése

A [párhuzamos indexelés,](search-howto-large-index.md)helyezze az adatokat több tárolóba vagy több virtuális mappák ugyanabban a tárolóban. Ezután hozzon létre több adatforrás- és indexelő-párt. Minden indexelők használhatja ugyanazt skillset és írja be az azonos cél keresési index, így a keresési alkalmazás nem kell tisztában ezt a particionálás.
További információ: [Nagy adatkészletek indexelése](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Lásd még
+ [Rövid útmutató: AI-dúsítási folyamat létrehozása a portálon](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: AI-dúsítási REST API-k megismerése](cognitive-search-tutorial-blob.md)
+ [Adatforrás-hitelesítő adatok megadása](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Nagy adatkészletek indexelése](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Bővített mezők hozzárendelése indexhez](cognitive-search-output-field-mapping.md)
