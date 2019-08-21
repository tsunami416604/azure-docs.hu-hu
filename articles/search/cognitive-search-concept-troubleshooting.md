---
title: Hibaelhárítási tippek a kognitív kereséshez – Azure Search
description: Tippek és hibaelhárítás a kognitív keresési folyamatok Azure Search-ban való beállításához.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.subservice: cognitive-search
ms.workload: search
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: luisca
ms.openlocfilehash: f006b018233d33ee4f8bf40f0de5ed0d9b4c4974
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639884"
---
# <a name="troubleshooting-tips-for-cognitive-search"></a>Hibaelhárítási tippek a kognitív kereséshez

Ez a cikk azokat a tippeket és trükköket tartalmazza, amelyek a Azure Search kognitív keresési képességeinek megismeréséhez szükségesek. 

Ha még nem tette meg, ugorjon végig az [oktatóanyagban: Ismerje meg, hogyan hívhatja meg a](cognitive-search-quickstart-blob.md) kognitív keresési API-kat a kognitív keresési gazdagítás blob-adatforrásokra való alkalmazása során.

## <a name="tip-1-start-with-a-small-dataset"></a>1\. tipp: Kezdés kis adatkészlettel
A problémák gyors megtalálásának legjobb módja az, hogy növelje a hibák elhárításának sebességét. Az indexelési idő csökkentésének legjobb módja az indexelt dokumentumok számának csökkentése. 

Első lépésként hozzon létre egy adatforrást mindössze néhány dokumentum/rekord használatával. A dokumentum mintájának jól láthatónak kell lennie az indexelt dokumentumok különböző részein. 

Futtassa a dokumentum mintáját a végpontok közötti folyamaton, és győződjön meg arról, hogy az eredmények megfelelnek az igényeinek. Ha elégedett az eredménnyel, további fájlokat adhat hozzá az adatforráshoz.

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>2\. tipp: Ellenőrizze, hogy helyesek-e az adatforrás hitelesítő adatai
Az adatforrás-kapcsolatok nem lettek érvényesítve, amíg meg nem határoz egy olyan indexelő, amelyik azt használja. Ha olyan hibák jelennek meg, amelyek megemlítik, hogy az indexelő nem tudja beolvasni az adatvesztést, ügyeljen a következőre:
- A kapcsolatok karakterlánca helyes. Ha SAS-jogkivonatokat hoz létre, akkor ügyeljen arra, hogy a Azure Search által várt formátumot használja. A [különböző támogatott formátumok megismeréséhez lásd](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials) : hitelesítő adatok megadása szakasz.
- Az indexelő tárolójának neve helyes.

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>3\. tipp: Megtudhatja, hogy mi működik, még akkor is, ha vannak hibák
Időnként előfordulhat, hogy egy kis hiba leállítja az indexelő a zeneszámokban. Ez rendben van, ha egyenként szeretné kijavítani a problémákat. Előfordulhat azonban, hogy figyelmen kívül hagyja az adott típusú hibát, így az indexelő továbbra is megtekintheti, hogy milyen folyamatok működnek valójában.

Ebben az esetben érdemes megállapítania, hogy az indexelő figyelmen kívül hagyja a hibákat. Ezt úgy teheti meg, hogy a *maxFailedItems* és a *maxFailedItemsPerBatch* értéket a-1 értékre állítja be az indexelő definíciójának részeként.

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
## <a name="tip-4-looking-at-enriched-documents-under-the-hood"></a>4\. tipp: A kibővített dokumentumokat a motorháztető alatt tekintheti meg 
A dúsított dokumentumok a dúsítás során létrehozott ideiglenes struktúrák, majd a feldolgozás befejezésekor törlődnek.

Ha pillanatképet szeretne készíteni az indexelés során létrejött bővített dokumentumról, adja hozzá az indexhez az ```enriched``` mezőt. Az indexelő automatikusan hozzáadja a mezőhöz az adott dokumentum bővítéseinek karakterláncos leképezését.

Az ```enriched``` mező egy sztringet tartalmaz, amely a JSON-ban szereplő memóriabeli bővített dokumentum logikai leképezése.  A mező értéke azonban egy érvényes JSON-dokumentum. Mivel az idézőjelek előtt escape-karakter áll, a `\"` karaktereket `"` karakterre kell cserélnie, ha a dokumentumot formázott JSON-ként szeretné megtekinteni. 

A dúsított mező csak hibakeresési célokat szolgál, így könnyebben megismerheti annak a tartalomnak a logikai formáját, amellyel a kifejezéseket kiértékeli a rendszer. Ez a mező nem függhet indexelési célokra.

Adjon hozzá ```enriched``` egy mezőt az index definíciójának részeként a hibakereséshez:

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

## <a name="tip-5-expected-content-fails-to-appear"></a>5\. tipp: A várt tartalom nem jelenik meg

A hiányzó tartalom az indexelés során eldobott dokumentumok eredménye lehet. Az ingyenes és az alapszintű csomagok esetében a dokumentumok mérete alacsony. Az indexelés során a korlátot meghaladó fájlok el lettek dobva. Az eldobott dokumentumokat a Azure Portalban tekintheti meg. A Search szolgáltatás Irányítópultján kattintson duplán az indexelő csempére. Tekintse át a sikeres dokumentumok indexelésének arányát. Ha az érték nem 100%, akkor további részletekért kattintson az arányra. 

Ha a probléma a fájlmérethez kapcsolódik, a következőhöz hasonló hibaüzenet jelenhet meg: "A blob \<fájlneve >" a \<fájl mérete > bájtok mérete, ami meghaladja az aktuális szolgáltatási réteghez tartozó dokumentumok kinyerésének maximális méretét. " Az indexelő korlátaival kapcsolatos további információkért lásd: [szolgáltatási korlátok](search-limits-quotas-capacity.md).

A tartalom nem jelenik meg egy második oka, hogy kapcsolódó bemeneti/kimeneti leképezési hibák merülhetnek fel. Például a kimeneti cél neve "People", de az index mező neve kisbetűs "emberek". A rendszer 201 sikeres üzeneteket adhat vissza a teljes folyamathoz, így úgy gondolja, hogy sikeres volt az indexelés, ha valójában egy mező üres. 

## <a name="tip-6-extend-processing-beyond-maximum-run-time-24-hour-window"></a>6\. tipp: A maximális futási időn túli feldolgozás kiterjesztése (24 órás időszak)

A képelemzés még egyszerű esetekre is számításba vehető, így ha a képek különösen nagyok vagy bonyolultak, a feldolgozási idő túllépheti a maximálisan engedélyezett időt. 

A maximális futási idő a következő szintektől függ: több perc az ingyenes szinten, a számlázható rétegek 24 órás indexelése. Ha a feldolgozás nem hajtható végre egy 24 órás időszakon belül az igény szerinti feldolgozáshoz, váltson ütemezésre, hogy az indexelő feldolgozza a feldolgozást, ahol abbahagyta. 

Az ütemezett indexek esetében az indexelés az utolsó ismert jó dokumentumon folytatja az ütemezést. Ismétlődő ütemezés használatával az indexelő az összes nem feldolgozott lemezkép feldolgozásának idejére áttekintheti az adott lemezképen keresztüli várakozó képeket. Az ütemezett szintaxissal kapcsolatos további információkért lásd [a 3. lépést: Hozzon létre egy-Indexer](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer) -t, vagy tekintse [meg, hogyan ütemezhet Azure Search indexelő](search-howto-schedule-indexers.md).

> [!NOTE]
> Ha egy indexelő egy bizonyos ütemezésre van beállítva, de többször is meghibásodik ugyanazon a dokumentumon, és minden egyes futtatásakor újra leáll, az indexelő egy ritkábban (legfeljebb legalább 24 óránként) fog futni, amíg a folyamat sikeresen elvégezte az előrehaladást. a.  Ha úgy gondolja, hogy rögzítette a problémát, amely miatt az indexelő egy bizonyos ponton elakadt, igény szerint futtathatja az Indexelő szolgáltatást, és ha a folyamat sikeresen elvégezte az előrehaladást, az indexelő az ütemezési intervallumba ismét visszatér.

A portálon alapuló indexeléshez (a gyors útmutatóban leírtak szerint) a "Futtatás egyszer" indexelő beállítás a feldolgozást`"maxRunTime": "PT1H"`1 órára korlátozza (). Előfordulhat, hogy hosszabb időre szeretné kiterjeszteni a feldolgozási ablakot.

## <a name="tip-7-increase-indexing-throughput"></a>7\. tipp: Az indexelési teljesítmény növelése

[Párhuzamos indexeléshez](search-howto-large-index.md)helyezze az adatait több tárolóba vagy több virtuális mappába ugyanabban a tárolóban. Ezután hozzon létre több DataSource és indexelő párokat. Az összes indexelő használhatja ugyanazt a készségkészlet, és az azonos keresési indexbe írhat, így a keresési alkalmazásnak nem kell megismernie ezt a particionálást.
További információ: [nagyméretű](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)adathalmazok indexelése.

## <a name="see-also"></a>Lásd még
+ [Rövid útmutató: Kognitív keresési folyamat létrehozása a portálon](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: A kognitív keresési REST API-k megismerése](cognitive-search-tutorial-blob.md)
+ [Adatforráshoz tartozó hitelesítő adatok megadása](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [Nagyméretű adathalmazok indexelése](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [A dúsított mezők indexhez való leképezése](cognitive-search-output-field-mapping.md)
