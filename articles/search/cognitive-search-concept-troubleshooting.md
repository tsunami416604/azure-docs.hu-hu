---
title: Hibaelhárítási tippek a kognitív keresés – Azure Search
description: Tippek és hibaelhárítás beállításának kognitív keresési folyamatok az Azure Search szolgáltatásban.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: ebc0ca718ab8edf5ef644993c71b0353861265b8
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961847"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Hibaelhárítási tippek a kognitív keresés

Ez a cikk tippeket és trükköket, így mindig áthelyezése, Ismerkedés az Azure Search cognitive search funkcióira listáját tartalmazza. 

Ha még nem tette meg, elvégezhető a [oktatóanyag: A cognitive search API-k hívása](cognitive-search-quickstart-blob.md) az eljárás a kognitív keresés végrehajtott információbeolvasás alkalmazása egy blob adatforráshoz.

## <a name="tip-1-start-with-a-small-dataset"></a>1. tipp: Kezdje egy kisebb adatkészletet
Problémák gyorsan megtalálhatja a legjobb módja, hogy milyen problémák megoldásához sebessége növelhető. A legjobb módszer az indexelési idő csökkentése érdekében van indexelendő dokumentumok számának csökkentésével. 

Először hozzon létre egy adatforrást az csak néhány rekordra dokumentumok /. A dokumentum mintát kell lennie a dokumentumok indexelését, számos jó reprezentációját. 

A dokumentum minta haladjon végig a teljes körű folyamatot, és ellenőrizze, hogy az eredmények megfelelnek-e az igényeinek. Ha elégedett az eredménnyel, az adatforrás is hozzáadhat további fájlokat.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>2. tipp: Ellenőrizze, hogy helyesek-e az adatforrás azonosító adatai
Az adatforrás-kapcsolatot határoz meg az indexelő által használt, amíg nincs érvényesítve. Ha hibákat megemlíteni, hogy az indexelő nem tudja beolvasni az adatokat látja, ellenőrizze, hogy:
- A kapcsolati karakterlánc helyességéről. Kifejezetten, amikor a SAS-jogkivonatokat hoz létre, ügyeljen arra, hogy az Azure Search által várt formátumot használja. Lásd: [megadása hitelesítő adatok szakaszban](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) további információ a különböző formátumokban támogatja.
- A tároló nevére, az indexelő a helyességéről.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>3. tipp: Mi működik, akkor sem, ha bizonyos hibák
Néha a kis hiba annak nyomon követi az indexelő leáll. Ez megfelelő, ha azt tervezi, hogy egyenként problémák megoldása. Azonban érdemes figyelmen kívül egy adott típusú hiba, lehetővé téve az indexelő, így láthatja, milyen folyamatok ténylegesen dolgozik a folytatáshoz.

Ebben az esetben érdemes állapítható meg, hogy az indexelő figyelmen kívül hagyja a hibákat. Teheti meg, hogy *maxFailedItems* és *maxFailedItemsPerBatch* mint -1 részeként az indexelő definíciója.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>4. tipp: Megnézzük a továbbfejlesztett dokumentumok technikai részletek 
Továbbfejlesztett dokumentumok olyan ideiglenes struktúrák Adatbővítés során létrehozott, majd törli a feldolgozás befejezésekor.

Ha pillanatképet szeretne készíteni az indexelés során létrejött bővített dokumentumról, adja hozzá az indexhez az ```enriched``` mezőt. Az indexelő automatikusan hozzáadja a mezőhöz az adott dokumentum bővítéseinek karakterláncos leképezését.

Az ```enriched``` mező egy sztringet tartalmaz, amely a JSON-ban szereplő memóriabeli bővített dokumentum logikai leképezése.  A mező értéke azonban egy érvényes JSON-dokumentum. Mivel az idézőjelek előtt escape-karakter áll, a `\"` karaktereket `"` karakterre kell cserélnie, ha a dokumentumot formázott JSON-ként szeretné megtekinteni. 

A képi elemekben gazdag mező segítségével megismerheti a tartalmat a kifejezések alapján vannak kiértékelt logikai felépítésének csupán, hibakeresés szól. Ez a mező az indexelés céljából nem függhet.

Adjon hozzá egy ```enriched``` hibakeresési célokra az index definícióját részeként mező:

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

## <a name="tip-5-expected-content-fails-to-appear"></a>5. tipp: A várt tartalom nem jelennek meg

Hiányzó tartalom az indexelés során megszakad dokumentumok oka lehet. Ingyenes és alapszintű csomag kis korlátokkal rendelkeznek a dokumentumok méretétől. Minden olyan fájlt, a korlát túllépése az indexelés során megszakad. Az Azure Portalon az eldobott dokumentumok ellenőrizheti. A search szolgáltatás irányítópultján kattintson duplán az indexelő csempére. Tekintse át a sikeres indexelt dokumentumok aránya. Ha nem 100 %-os, kattinthat a arány részletesebb megjelenítéséhez. 

Ha a fájl mérete kapcsolatos a probléma, ehhez hasonló hibaüzenetet jelenhetnek meg: "A blob < fájlnév >" van < fájlméret-> (bájt) meghaladja a maximális méretet, a dokumentum kinyerési aktuális szolgáltatásszint méretét." Az indexelő korlátok további információkért lásd: [szolgáltatási korlátozásaival](search-limits-quotas-capacity.md).

Egy második oka nem jelenik meg a tartalom lehet a kapcsolódó bemeneti/kimeneti leképezési hibák. Például egy kimeneti cél neve "Felhasználók" viszont az index a mező neve kisbetűket "felhasználók". A rendszer a teljes folyamat 201 sikeres üzenetek eredményezhetnek, ezért úgy gondolja, hogy az indexelés sikeres, ha valójában egy mező üres. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>6. tipp: Kiterjesztheti a feldolgozási meghaladja a maximálisan engedélyezett futási időt (24 órás időszak)

Képelemzés nagy számítási igényű olyan esetekben, akár egyszerű, így rendszerképekkel különösen nagyok vagy összetettek lennének, amikor feldolgozási időt lépheti túl az engedélyezett maximális idő. 

Maximálisan engedélyezett futási időt díjcsomag szerint változik: több percet az ingyenes szinten, 24 órás indexelést a számlázható szint. Ha feldolgozása nem lehetett végrehajtani, igény szerinti feldolgozási egy 24 órás időtartamon belül, váltson egy ütemezést az indexelővel feldolgozási csomópontmetrikák felvételéhez, ahol abbahagyta. 

Az ütemezett indexelők esetében az indexelés folytatja, az utolsó ismert jó dokumentum ütemezés szerint. Az ismétlődő ütemezés szerint, az indexelő órák vagy napok, nem feldolgozott összes rendszerkép feldolgozásáig sorozatát keresztül működik korongot keresztül a lemezkép várakozó fájlok számát. Az ütemezés szintaxisa további információkért lásd: [3. lépés: Az-indexelő létrehozása](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer).

Portal-alapú az indexelés (mint a rövid útmutatóban leírtak szerint), az "egyszeri futtatás" indexelő kiválasztása lehetőséget 1 órára feldolgozási korlátok (`"maxRunTime": "PT1H"`). Előfordulhat, hogy szeretné valamilyen hosszabb feldolgozási időszakának kiterjesztése.

## <a name="tip-7-increase-indexing-throughput"></a>7. tipp: Az indexelő átviteli sebesség növelése

A [párhuzamos indexelő](search-howto-large-index.md), az adatok helyezzen több tároló vagy több virtuális mappákban belül ugyanazt a tárolót. Ezután hozzon létre több adatforrás és az indexelő párt. Minden indexelő esetében azonos készségeitől használhatja, és írja az azonos cél keresési indexhez, így a fájlkeresés alkalmazás nem kell figyelembe venni ezt a particionálása.
További információkért lásd: [nagy méretű adatkészletek indexelő](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets).

## <a name="see-also"></a>Lásd még
+ [Rövid útmutató: A kognitív keresés folyamat létrehozása a portálon](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: További tudnivalók a cognitive search REST API-k](cognitive-search-tutorial-blob.md)
+ [Az adatforrás hitelesítő adatainak megadása](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Az indexelő nagyméretű adatkészletek](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Hogyan hidaljuk mezők leképezése egy indexbe](cognitive-search-output-field-mapping.md)
