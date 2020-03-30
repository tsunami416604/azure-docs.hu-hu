---
title: Növekményes dúsítás (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Gyorsítótárazhatja a köztes tartalmat és a növekményes módosításokat az AI-bővítési folyamatból az Azure Storage-ban a meglévő feldolgozott dokumentumokba történő befektetések megőrzése érdekében. Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024143"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Bevezetés a növekményes bővítésbe és gyorsítótárazásba az Azure Cognitive Search szolgáltatásban

> [!IMPORTANT] 
> A növekményes bővítés jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) A [REST API 2019-05-06-Preview verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs portál- vagy .NET SDK-támogatás.

A növekményes dúsítás gyorsítótárazást és állapotmegőrzési lehetőséget ad hozzá a dúsítási folyamathoz, megőrizve a meglévő kimenetbe történő befektetést, miközben csak az adott módosítás által érintett dokumentumokat módosítja. Ez nem csak a feldolgozásba (különösen az OCR-be és a képfeldolgozásba) történő monetáris befektetést őrzi meg, hanem hatékonyabb rendszert is biztosít. Struktúrák és tartalom gyorsítótárazása kor az indexelő meghatározhatja, hogy mely képességek változtak meg, és csak a módosított képességeket futtathatja, valamint az alsóbb rétegbeli függő képességeket. 

## <a name="indexer-cache"></a>Indexelő gyorsítótára

A növekményes bővítés gyorsítótárat ad a dúsítási folyamathoz. Az indexelő gyorsítótárazza a dokumentumfeltörés eredményeit, valamint az egyes szakértelem kimeneteit minden dokumentumhoz. Egy skillset frissítése esetén csak a módosított vagy a későbbi, a szakértelem újrafuttatása lesz. A frissített eredmények a gyorsítótárba kerülnek, és a dokumentum frissül a keresési indexben vagy a tudástárolóban.

Fizikailag a gyorsítótár egy blob tárolóban van tárolva az Azure Storage-fiókban. A gyorsítótár táblatárolót is használ a frissítések feldolgozásának belső rekordjaként. A keresési szolgáltatáson belüli összes index ugyanazt a tárfiókot is megoszthatja az indexelő gyorsítótárához. Minden indexelő egyedi és nem módosítható gyorsítótár-azonosítót rendel a használt tárolóhoz.

## <a name="cache-configuration"></a>Gyorsítótár konfigurációja

Be kell állítania `cache` a tulajdonságot az indexelőn, hogy elkezdhesse a növekményes dúsítás előnyeit. A következő példa egy olyan indexelőt mutat be, amelyen engedélyezve van a gyorsítótárazás. A konfiguráció egyes részeit a következő szakaszok ismertetik. További információ: [Növekményes bővítés beállítása](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Ha ezt a tulajdonságot egy meglévő indexelőre állítja be, alaphelyzetbe állítja és újrafuttatja az indexelőt, így az adatforrás összes dokumentuma újra feldolgozásra kerül. Ez a lépés a skillset korábbi verzióival bővített dokumentumok kiküszöböléséhez szükséges. 

## <a name="cache-management"></a>Gyorsítótár-kezelés

A gyorsítótár életciklusát az indexelő kezeli. Ha `cache` az indexelő tulajdonsága null értékű, vagy a kapcsolati karakterlánc megváltozik, a meglévő gyorsítótár törlődik a következő indexelő futtatáskor. A gyorsítótár életciklusa is az indexelő életciklusához van kötve. Ha egy indexelő törlődik, a társított gyorsítótár is törlődik.

Bár a növekményes bővítés célja, hogy az Ön beavatkozása nélkül észlelje és reagáljon a változásokra, vannak olyan paraméterek, amelyek segítségével felülbírálhatja az alapértelmezett viselkedést:

+ Új dokumentumok fontossági sorrendbe állítása
+ Szakértelem-ellenőrzések megkerülése
+ Adatforrás-ellenőrzések megkerülése
+ Force skillset értékelése

### <a name="prioritize-new-documents"></a>Új dokumentumok fontossági sorrendbe állítása

Állítsa `enableReprocessing` be, hogy a tulajdonság szabályozza a gyorsítótárban már képviselt bejövő dokumentumok feldolgozását. Ha `true` (alapértelmezett), a gyorsítótárban már lévő dokumentumok újra feldolgozása az indexelő újrafuttatásakor, feltéve, hogy a szakértelem frissítése hatással van a dokumentumra. 

Ha `false`a meglévő dokumentumokat nem dolgozza fel újra, hatékonyan rangsorolja az új, bejövő tartalmat a meglévő tartalommal szemben. Csak ideiglenesen `enableReprocessing` `false` kell beállítani. A korpusz közötti konzisztencia biztosítása érdekében az idő nagy részében biztosítani `enableReprocessing` `true` kell, hogy minden új és meglévő dokumentum érvényes legyen a jelenlegi skillset definíció szerint.

### <a name="bypass-skillset-evaluation"></a>A skillset értékelésének megkerülése

A skillset módosítása és az adott skillset újrafeldolgozása általában kéz a kézben jár. Azonban a skillset néhány módosítása nem eredményezheti az újrafeldolgozást (például egy egyéni szakértelem üzembe helyezése egy új helyre vagy egy új hozzáférési kulccsal). Ezek valószínűleg olyan perifériás módosítások, amelyek nek nincs valódi hatása a skillset anyagára. 

Ha tudja, hogy a skillset módosítása valóban felületes, felül kell `disableCacheReprocessingChangeDetection` írnia a skillset kiértékelését a paraméter `true`beállításával:

1. Hívja meg a Frissítési képességekkészletet, és módosítsa a skillset definícióját.
1. Fűzz `disableCacheReprocessingChangeDetection=true` hozzá a paraméterhez a kéréshez.
1. Küldje el a módosítást.

Ez a paraméter beállítása biztosítja, hogy csak a skillset definíciójának frissítései legyenek véglegesítve, és a módosítás ne legyen kiértékelve a meglévő corpus ra gyakorolt hatásokra.

A következő példa egy Frissítési Skillset-kérést mutat be a paraméterrel:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Adatforrás-érvényesítési ellenőrzések megkerülése

Az adatforrás-definíció legtöbb módosítása érvényteleníti a gyorsítótárat. Azonban olyan esetekben, ahol tudja, hogy a változás nem érvénytelenítheti a gyorsítótárat – például egy kapcsolati`ignoreResetRequirement` karakterlánc módosítása vagy a tárfiók kulcsának elforgatása – hozzáfűzi a paramétert az adatforrás-frissítéshez. Ha ezt `true` a paramétert úgy állítja be, hogy a véglegesítés átmenjen, anélkül, hogy olyan alaphelyzetbe állítási feltételt indítana el, amely az összes objektum újraépítését és feltöltését eredményezné.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Force skillset értékelése

A gyorsítótár célja, hogy elkerülje a szükségtelen feldolgozást, de tegyük fel, hogy módosítja a szakértelem, amely az indexelő nem észleli (például a külső kód módosítása, például egy egyéni szakértelem).

Ebben az esetben [használhatja](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) a reset képességek egy adott szakértelem újrafeldolgozásának kényszerítésére, beleértve az okat a szakértelem kimenetétől függő alsóbb rétegbeli képességeket is. Ez az API elfogad egy POST-kérelmet, amely tartalmazza azokat a képességeket, amelyeket érvényteleníteni kell, és újrafeldolgozásra meg kell jelölni. A képességek alaphelyzetbe állítása után futtassa az indexelőt a folyamat meghívásához.

## <a name="change-detection"></a>Változásészlelés

Miután engedélyezte a gyorsítótárat, az indexelő kiértékeli a folyamatösszeállítás változásait annak meghatározásához, hogy mely tartalom használható fel újra, és mely újrafeldolgozást igényel. Ez a szakasz felsorolja azokat a módosításokat, amelyek véglegesen érvénytelenítik a gyorsítótárat, majd olyan módosításokat, amelyek növekményes feldolgozást váltanak ki. 

### <a name="changes-that-invalidate-the-cache"></a>A gyorsítótárat érvénytelenítő módosítások

Az érvénytelenítő módosítás olyan, ahol a teljes gyorsítótár már nem érvényes. Az érvénytelenítő módosításra példa az adatforrás frissítése. Itt van a teljes lista a változások, amelyek érvénytelenítik a cache:

* Váltás az adatforrás típusára
* Váltás adatforrás-tárolóra
* Adatforráshoz tartozó hitelesítő adatok
* Adatforrás-változásészlelési házirend
* Adatforrások törlési észlelési házirendje
* Indexelő mezőleképezései
* Indexelő paraméterek
    * Elemzési mód
    * Kizárt fájlnév-kiterjesztések
    * Indexelt fájlnév-kiterjesztések
    * Csak a túlméretes dokumentumok tárolási metaadatainak indexelése
    * Tagolt szövegfejlécek
    * Tagolt szöveghatároló
    * Dokumentum gyökér
    * Képművelet (a képek kibontásának módosítása)

### <a name="changes-that-trigger-incremental-processing"></a>Növekményes feldolgozást kiváltó módosítások

Növekményes feldolgozás kiértékeli a skillset definícióját, és meghatározza, hogy mely képességek et kell újrafuttatni, szelektíven frissíti a dokumentumfa érintett részeit. Itt van a teljes lista a változások at eredményező növekményes dúsítás:

* A skillset-ben a skill-nek más típusa van. A szakértelem odata típusa frissül
* A szakértelem-specifikus paraméterek frissülnek, például az URL-cím, az alapértelmezett értékek vagy más paraméterek
* A szakértelem kimenete megváltozik, a szakértelem további vagy különböző kimeneteket ad vissza
* Az ebből eredő készségfrissítések különböző származásúak, a készségek láncolása megváltozott, azaz szakértelem-bevitel
* Bármely upstream szakértelem érvénytelenítése, ha egy olyan szakértelem, amely ehhez a szakértelemhez bemenetet biztosít, frissül
* A tudástároló vetítési helyének frissítése, dokumentumok újravetítése
* Változások a tudástároló-előrejelzésekben, dokumentumok újravetítését eredményezik
* Az indexelőn módosított kimeneti mezőleképezések eredménye a dokumentumok indexbe való újravetítése

## <a name="api-reference"></a>API-referencia

A REST `2019-05-06-Preview` API-verzió növekményes bővítést biztosít az indexelők, a skillsetek és az adatforrások további tulajdonságai révén. A referenciadokumentációmellett [lásd: A gyorsítótárazás konfigurálása növekményes bővítéshez](search-howto-incremental-index.md) az API-k hívásának részleteiről.

+ [Indexelő létrehozása (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Indexelő frissítése (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Skillset frissítése (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (új URI-paraméter a kérelemben)

+ [Reset Skills (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Adatbázis-indexelők (Azure SQL, Cosmos DB). Egyes indexelők lekérdezéseken keresztül olvassa le az adatokat. Az adatokat lekérő lekérdezések esetén [az adatforrás frissítése](https://docs.microsoft.com/rest/api/searchservice/update-data-source) egy új paramétert támogat a **ignoreResetRequirement**kérésen, amelyet akkor kell beállítani, `true` amikor a frissítési művelet nem érvényteleníti a gyorsítótárat. 

  Használja **az ignoreResetRequirement-t** takarékosan, mivel nem kívánt inkonzisztenciához vezethet az adatokban, amelyet a rendszer nem fog könnyen észlelni.

## <a name="next-steps"></a>További lépések

A növekményes bővítés egy hatékony funkció, amely kiterjeszti a változások nyomon követését a skillsets-re és a a i-gazdagításra. Az AIncremental dúsítása lehetővé teszi a meglévő feldolgozott tartalom újrafelhasználását a skillset tervezés során.

Következő lépésként engedélyezze a gyorsítótárazást egy meglévő indexelőn, vagy adjon hozzá egy gyorsítótárat egy új indexelő definiálásakor.

> [!div class="nextstepaction"]
> [Gyorsítótárazás konfigurálása növekményes dúsításhoz](search-howto-incremental-index.md)
