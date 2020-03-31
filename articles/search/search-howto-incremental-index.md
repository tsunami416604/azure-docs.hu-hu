---
title: Gyorsítótár és növekményes bővítés konfigurálása (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Engedélyezze a gyorsítótárazást, és őrizze meg a dúsított tartalom állapotát a kognitív képességek ellenőrzött feldolgozásához. Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 66bac2a063a3257a2101ca2f30e5946264adb9ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989552"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>A gyorsítótárazás konfigurálása növekményes bővítéshez az Azure Cognitive Search szolgáltatásban

> [!IMPORTANT] 
> A növekményes bővítés jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) A [REST API 2019-05-06-Preview verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs portál- vagy .NET SDK-támogatás.

Ez a cikk bemutatja, hogyan adhat hozzá gyorsítótárazást egy dúsítási folyamathoz, hogy fokozatosan módosíthassa a lépéseket anélkül, hogy minden alkalommal újra kellene építenie. Alapértelmezés szerint a skillset állapot nélküli, és az összetétel bármely részének módosítása az indexelő teljes ismétlését igényli. A növekményes bővítéssel az indexelő meghatározhatja, hogy a dokumentumfa mely részeit kell frissíteni a skillset vagy indexelő definíciókban észlelt változások alapján. A meglévő feldolgozott kimenet megmarad, és lehetőség szerint újra felhasználható. 

A gyorsítótárazott tartalom az Azure Storage-ban található az Ön által megadott fiókadatok alapján. A tároló, `ms-az-search-indexercache-<alpha-numerc-string>`nevű, jön létre, amikor az indexelő futtatásakor. A keresési szolgáltatás által kezelt belső összetevőnek kell tekinteni, és nem szabad módosítani.

Ha nem ismeri az indexelők beállítását, kezdje az [indexelő áttekintésével,](search-indexer-overview.md) majd folytassa a [skillsets-t](cognitive-search-working-with-skillsets.md) a dúsítási folyamatok megismeréséhez. A legfontosabb fogalmak hátterét a [növekményes bővítés című témakörben tetszés ben láthatja.](cognitive-search-incremental-indexing-conceptual.md)

## <a name="enable-caching-on-an-existing-indexer"></a>Gyorsítótárazás engedélyezése meglévő indexelőn

Ha egy meglévő indexelő, amely már rendelkezik egy skillset, kövesse az ebben a szakaszban a gyorsítótárazás hozzáadásához. Egyszeri műveletként alaphelyzetbe kell állítania, és újra kell futtatnia az indexelőt teljes egészében, mielőtt a növekményes feldolgozás érvénybe léphetne.

> [!TIP]
> A koncepció igazolásaként futva futtasson ezen a [portálon gyorsindítással](cognitive-search-quickstart-blob.md) a szükséges objektumok létrehozásához, majd a Postman vagy a portál segítségével készítse el a frissítéseket. Előfordulhat, hogy szeretne csatolni egy számlázható Cognitive Services-erőforrást. Ha az indexelőt többször futtatja, kimeríti az ingyenes napi foglalást, mielőtt az összes lépést végrehajtana.

### <a name="step-1-get-the-indexer-definition"></a>1. lépés: Az indexelő definíciójának bekése

Kezdje egy érvényes, meglévő indexelővel, amely rendelkezik ezekkel az összetevőkkel: adatforrás, skillset, index. Az indexelőnek futtathatónak kell lennie. 

Egy API-ügyfél használatával hozzon létre egy [GET Indexer-kérelmet](https://docs.microsoft.com/rest/api/searchservice/get-indexer) az indexelő aktuális konfigurációjának lekéréséhez. Ha az előzetes API-verziót használja a `cache` GET indexelőhöz, a definíciók hoz egy null értékű tulajdonságot ad hozzá.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Másolja az indexelő definícióját a válaszból.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>2. lépés: A gyorsítótár-tulajdonság módosítása az indexelő definíciójában

Alapértelmezés szerint `cache` a tulajdonság null értékű. A gyorsítótár-konfiguráció beállításához használjon API-ügyfelet (a portál nem támogatja ezt a részecskefrissítést). 

Módosítsa a gyorsítótár-objektumot úgy, hogy az tartalmazza a következő szükséges és választható tulajdonságokat: 

+ A `storageConnectionString` szükséges, és be kell állítani egy Azure storage-kapcsolati karakterlánc. 
+ A `enableReprocessing` logikai tulajdonság`true` megadása nem kötelező ( alapértelmezés szerint), és azt jelzi, hogy a növekményes bővítés engedélyezve van. Szükség esetén beállíthatja, `false` hogy függessze fel a növekményes feldolgozást, miközben más erőforrás-igényes műveletek, például az új dokumentumok indexelése folyamatban van, majd visszafordíthatja későbbre. `true`

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>3. lépés: Az indexelő alaphelyzetbe állítása

Az indexelő alaphelyzetbe állítása szükséges a meglévő indexelők növekményes dúsításának beállításakor annak biztosítása érdekében, hogy az összes dokumentum konzisztens állapotban legyen. Használhatja a portálvagy egy API-ügyfél és az [Indexelés REST API-t](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) ehhez a feladathoz.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>4. lépés: A frissített definíció mentése

[Frissítse az indexelőt](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) put kéréssel, a kérelem törzsének tartalmaznia kell a gyorsítótár-tulajdonsággal rendelkező frissített indexelő-definíciót. Ha 400-as t kap, ellenőrizze az indexelő definícióját, és győződjön meg arról, hogy minden követelmény teljesül (adatforrás, skillset, index).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Ha most újabb GET-kérelmet ad ki az indexelőn, a szolgáltatás válasza tartalmaz egy `ID` tulajdonságot a gyorsítótár-objektumban. Az alfanumerikus karakterlánc hozzáfűzi a tároló nevét, amely tartalmazza az összes gyorsítótárazott eredményt és az indexelő által feldolgozott dokumentumok köztes állapotát. Az azonosító t használja a blob storage gyorsítótárának egyedi elnevezésére.

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>5. lépés: Az indexelő futtatása

Az indexelő futtatásához használhatja a portált vagy az API-t. A portálon az indexelők listájában jelölje ki az indexelőt, és kattintson a **Futtatás**gombra. A portál használatának egyik előnye, hogy figyelheti az indexelő állapotát, feljegyezheti a feladat időtartamát és a feldolgozáshoz szükséges dokumentumok nagy részét. A portáloldalak néhány percenként frissülnek.

Másik lehetőségként a REST segítségével [futtathatja az indexelőt:](https://docs.microsoft.com/rest/api/searchservice/run-indexer)

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Az indexelő futtatása után megtalálhatja a gyorsítótárat az Azure Blob storage-ban. A tároló neve a következő formátumú:`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Az indexelő alaphelyzetbe állítása és újrafuttatása teljes újraépítést eredményez, így a tartalom gyorsítótárazható. Minden kognitív dúsítást újra futtatunk az összes dokumentumon.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>6. lépés: A skillset módosítása és a növekményes dúsítás megerősítése

A skillset módosításához használhatja a portált vagy az API-t. Ha például szövegfordítást használ, egy egyszerű `en` szövegközi módosítás egy másik nyelvről `es` vagy egy másik nyelvre elegendő a növekményes dúsítás koncepcióigazolásához.

Futtassa újra az indexelőt. Csak a bővített dokumentumfa azon részei frissülnek. Ha a [portál rövid útmutatóját](cognitive-search-quickstart-blob.md) használta koncepcióigazolásként, és a szövegfordítási jártasságot "es"-re módosította, észre fogja venni, hogy az eredeti 14 helyett csak 8 dokumentum frissül. A fordítási folyamat által nem érintett képfájlokat a rendszer újra felhasználja a gyorsítótárból.

## <a name="enable-caching-on-new-indexers"></a>Gyorsítótárazás engedélyezése új indexelőkön

Egy új indexelő növekményes dúsításának beállításához mindössze annyit kell tennie, hogy az Indexelő definíciója hasznos adatában felveszi a `cache` tulajdonságot az [Indexer létrehozása (2019-05-06-preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer)hívásakor. 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Gyorsítótárazott kimenet ellenőrzése

A gyorsítótárat az indexelő hozta létre, használja és kezeli, és tartalma nem emberileg olvasható formátumban jelenik meg. A legjobb módszer annak megállapítására, hogy a gyorsítótár használata az indexelő futtatásával, és hasonlítsa össze a végrehajtási idő és a dokumentum számának előtte és utána metrikák. 

Tegyük fel például, hogy a beolvasott dokumentumok képelemzésével és optikai karakterfelismerésével (OCR) kezdődik, majd az eredményül kapott szöveg tovább-abszorbani elemzésével. Ha módosítja a későbbi szöveg szakértelem, az indexelő lekérheti az összes korábban feldolgozott kép- és OCR-tartalom a gyorsítótárból, frissítése és feldolgozása csak a szöveggel kapcsolatos módosítások at a módosításokat. Számíthat arra, hogy kevesebb dokumentumot fog látni a bizonylatok számában (például 8/8, szemben az eredeti futtatás 14/14-ével), rövidebb végrehajtási idővel és kevesebb költségre a számlán.

## <a name="working-with-the-cache"></a>A gyorsítótár használata

Miután a gyorsítótár működik, indexelők ellenőrizze a gyorsítótárat, amikor [az indexelő futtatása](https://docs.microsoft.com/rest/api/searchservice/run-indexer) van hívva, hogy mely részei a meglévő kimenet használható. 

Az alábbi táblázat összefoglalja, hogy a különböző API-k hogyan kapcsolódnak a gyorsítótárhoz:

| API           | Gyorsítótár hatása     |
|---------------|------------------|
| [Indexelő létrehozása (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) | Indexelőt hoz létre és futtat az első használatkor, beleértve a gyorsítótár létrehozását, ha az indexelő definíciója megadja. |
| [Indexelő futtatása](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | Szükség esetén dúsítási folyamatot hajt végre. Ez az API beolvassa a gyorsítótárból, ha létezik, vagy létrehoz egy gyorsítótárat, ha hozzáadta a gyorsítótárazást egy frissített indexelő definícióhoz. Ha olyan indexelőt futtat, amelynek engedélyezve van a gyorsítótárazás, az indexelő kihagyja a lépéseket, ha a gyorsítótárazott kimenet használható. Használhatja az api általánosan elérhető vagy előzetes verziójú API-verzióját.|
| [Indexelő alaphelyzetbe állítása](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| Törli az indexelő minden növekményes indexelési információ. A következő indexelő futtatása (akár igény szerinti, akár ütemezés) teljes újrafeldolgozás a semmiből, beleértve az összes szakértelem újrafuttatása és a gyorsítótár újraépítése. Funkcionálisan egyenértékű az indexelő törlésével és újbóli létrehozásával. Használhatja az api általánosan elérhető vagy előzetes verziójú API-verzióját.|
| [Készségek visszaállítása (2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) | Megadja, hogy mely képességeket kell újrafuttatni a következő indexelő futtatáskor, még akkor is, ha nem módosított a képességeket. A gyorsítótár ennek megfelelően frissül. A kimenetek, például a tudástároló vagy a keresési index, a gyorsítótárból újrafelhasználható adatok, valamint a frissített szakértelem szerint új tartalom használatával frissülnek. |

A gyorsítótárral való történiksség szabályozásáról a [Gyorsítótár kezelése című témakörben talál](cognitive-search-incremental-indexing-conceptual.md#cache-management)további információt.

## <a name="next-steps"></a>További lépések

A növekményes dúsítás olyan indexelőkre vonatkozik, amelyek skillseteket tartalmaznak. Következő lépésként látogasson el a skillset dokumentációba a fogalmak és az összetétel megértéséhez. 

Továbbá, ha engedélyezi a gyorsítótárat, érdemes tudni a paramétereket és API-kat, amelyek tényező a gyorsítótárazás, beleértve, hogyan felülbírálja vagy kényszeríti az adott viselkedést. További információt az alábbi hivatkozásokon talál.

+ [Skillset fogalmak és összetétele](cognitive-search-working-with-skillsets.md)
+ [Hogyan hozzunk létre egy skillset](cognitive-search-defining-skillset.md)
+ [Bevezetés a növekményes dúsításba és gyorsítótárazásba](cognitive-search-incremental-indexing-conceptual.md)
