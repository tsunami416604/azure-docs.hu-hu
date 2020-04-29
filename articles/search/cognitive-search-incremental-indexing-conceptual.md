---
title: Növekményes gazdagodás (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: A meglévő feldolgozott dokumentumok beruházásainak megőrzése érdekében gyorsítótárazza az AI-bővítési folyamat közbenső tartalmát és növekményes változásait az Azure Storage-ban. Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 09003c26ead9108d07ae339fcf64235c246474a4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77024143"
---
# <a name="introduction-to-incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Bevezetés a növekményes bővítés és a gyorsítótárazás az Azure-ban Cognitive Search

> [!IMPORTANT] 
> A növekményes gazdagodás jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nem érhető el portál vagy .NET SDK-támogatás.

A növekményes bővítés egy bővítési folyamathoz adja a gyorsítótárazást és a statefulness, megőrizve a meglévő kimenetbe való befektetését, miközben csak az adott módosítás által érintett dokumentumokat változtatja meg. Ez a művelet nem csupán a feldolgozásban (különösen az OCR-ben és a képfeldolgozásban) való pénzügyi beruházások megőrzését teszi lehetővé, ugyanakkor hatékonyabb rendszerre is vonatkozik. Ha a rendszer gyorsítótárazza a struktúrákat és a tartalmakat, az indexelő meghatározhatja, hogy mely készségek módosultak és fussanak, és csak azokat, amelyeket módosítottak, valamint az alsóbb rétegbeli függő képességeket is. 

## <a name="indexer-cache"></a>Indexelő gyorsítótár

A növekményes bővítés hozzáadja a gyorsítótárat a dúsítási folyamathoz. Az indexelő gyorsítótárazza az eredményeket a dokumentum repedésével együtt, valamint az egyes dokumentumok egyes képességeinek kimeneteit. A készségkészlet frissítésekor a rendszer csak a megváltozott vagy az alárendelt képességeket futtatja újra. A frissített eredményeket a rendszer a gyorsítótárba írja, és a dokumentum frissül a keresési indexben vagy a Knowledge Store-ban.

Fizikailag a gyorsítótár egy blob-tárolóban tárolódik az Azure Storage-fiókban. A gyorsítótár a táblázatos tárterületet is használja a feldolgozási frissítések belső rekordjához. A keresési szolgáltatáson belüli összes index ugyanazt a Storage-fiókot is megoszthatja az indexelő gyorsítótárban. Minden indexelő egy egyedi és nem módosítható gyorsítótár-azonosítót rendel hozzá az általa használt tárolóhoz.

## <a name="cache-configuration"></a>Gyorsítótár-konfiguráció

Az indexelő `cache` tulajdonságot úgy kell beállítani, hogy kihasználja a növekményes bővítésből származó kedvezményt. Az alábbi példa egy olyan indexelő mutat be, amelyen engedélyezve van a gyorsítótárazás. A konfiguráció adott részeit a következő szakasz ismerteti. További információt a [növekményes bővítés beállítása](search-howto-incremental-index.md)című témakörben talál.

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

Ha ezt a tulajdonságot egy meglévő indexelő eszközön szeretné beállítani, alaphelyzetbe kell állítania és újra kell futtatnia az indexelő, ami az adatforrásban lévő összes dokumentumot újra feldolgozza. Ez a lépés szükséges a készségkészlet korábbi verzióival dúsított dokumentumok eltávolításához. 

## <a name="cache-management"></a>Gyorsítótár-kezelés

A gyorsítótár életciklusát az indexelő kezeli. Ha az `cache` indexelő tulajdonsága NULL értékűre van állítva, vagy a kapcsolódási sztring módosul, a rendszer törli a meglévő gyorsítótárat a következő indexelő futtatáskor. A gyorsítótár életciklusa az indexelő életciklushoz is kötődik. Ha töröl egy indexelő, a társított gyorsítótár is törlődik.

Míg a növekményes bővítés úgy van kialakítva, hogy észlelje és reagáljon a változásokra az Ön részéről beavatkozás nélkül, vannak olyan paraméterek, amelyek segítségével felülbírálhatja az alapértelmezett viselkedéseket:

+ Új dokumentumok rangsorolása
+ Készségkészlet-ellenőrzések mellőzése
+ Adatforrás-ellenőrzések megkerülése
+ Készségkészlet kiértékelésének kényszerítése

### <a name="prioritize-new-documents"></a>Új dokumentumok rangsorolása

Állítsa be `enableReprocessing` a tulajdonságot a gyorsítótárban már megjelenő bejövő dokumentumok feldolgozásának szabályozására. Ha `true` az (alapértelmezett), a gyorsítótárban már meglévő dokumentumok újra fel lesznek dolgozva az indexelő újrafuttatásakor, feltéve, hogy a szakértelem frissítése hatással van erre a doc-ra. 

Ha `false`a meglévő dokumentumokat nem dolgozza fel újra, a meglévő tartalmakhoz képest gyakorlatilag új, bejövő tartalmakat rangsorol. Csak átmeneti alapon kell `enableReprocessing` beállítania `false` . A corpuson `enableReprocessing` keresztüli konzisztencia biztosításához az `true` idő nagy részének kell lennie, és biztosítania kell, hogy az összes új és létező dokumentum érvényes legyen az aktuális készségkészlet-definícióban.

### <a name="bypass-skillset-evaluation"></a>Kikerülő készségkészlet kiértékelése

A készségkészlet módosítása és a készségkészlet újrafeldolgozása jellemzően a kezét eredményezi. A készségkészlet módosításai azonban nem eredményezhetik az újrafeldolgozást (például egy egyéni képesség üzembe helyezését egy új helyre vagy egy új hozzáférési kulccsal). Legvalószínűbb, hogy ezek olyan perifériás módosítások, amelyeknek nincs valódi hatása a készségkészlet tartalmára. 

Ha tudja, hogy a készségkészlet változása valóban felszínes, érdemes felülbírálni a készségkészlet értékelését úgy, `disableCacheReprocessingChangeDetection` hogy `true`a paramétert a következőre állítja:

1. Hívja meg a frissítési Készségkészlet, és módosítsa a készségkészlet-definíciót.
1. Fűzze hozzá `disableCacheReprocessingChangeDetection=true` a paramétert a kérelemhez.
1. Küldje el a változást.

A paraméter beállítása biztosítja, hogy csak a készségkészlet-definíció frissítései legyenek véglegesítve, és a változás nem lesz kiértékelve a meglévő Corpus hatására.

Az alábbi példa egy frissítési Készségkészlet kérelmet mutat be a következő paraméterrel:

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Adatforrás-ellenőrzési ellenőrzések megkerülése

Az adatforrás-definíciók legtöbb módosítása érvényteleníti a gyorsítótárat. Ha azonban biztos lehet abban, hogy a módosítás nem érvényteleníti a gyorsítótárat – például a kapcsolatok karakterláncának módosítása vagy a kulcs elforgatása a Storage-fiókban – fűzze`ignoreResetRequirement` hozzá a paramétert az adatforrás frissítéséhez. Ha ezt a paramétert úgy állítja be, hogy `true` a véglegesítse a végrehajtást, anélkül, hogy olyan visszaállítási feltételt váltott ki, amely az összes objektum újraépítését és a semmiből való feltöltését eredményezi.

```http
PUT https://customerdemos.search.windows.net/datasources/callcenter-ds?api-version=2019-05-06-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Készségkészlet kiértékelésének kényszerítése

A gyorsítótár célja, hogy elkerülje a szükségtelen feldolgozást, de tegyük fel, hogy olyan képességet módosít, amelyet az indexelő nem érzékel (például egy másikat a külső kódban, például egy egyéni képességet).

Ebben az esetben a [képességek alaphelyzetbe állításával](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills) kényszerítheti az adott képesség újrafeldolgozását, beleértve az olyan alsóbb rétegbeli képességeket is, amelyek függőséggel rendelkeznek az adott szakértelem kimenetével. Ez az API egy POST-kérést fogad el azoknak a szakismereteknek a listájával, amelyeket érvényteleníteni kell, és meg kell adni az újrafeldolgozáshoz. A képességek alaphelyzetbe állítása után futtassa az indexelő a folyamat meghívásához.

## <a name="change-detection"></a>Változás észlelése

Miután engedélyezte a gyorsítótárat, az indexelő kiértékeli a folyamat-összeállítás változásait annak meghatározására, hogy mely tartalmak használhatók fel újra, és melyeket újra kell dolgozni. Ez a szakasz azokat a módosításokat sorolja fel, amelyek érvénytelenítik a gyorsítótárat, majd a növekményes feldolgozást kiváltó változások következnek. 

### <a name="changes-that-invalidate-the-cache"></a>A gyorsítótár érvénytelenítésének módosításai

Az érvénytelenítési változás egy olyan esetben, amikor a teljes gyorsítótár már nem érvényes. Az érvénytelenítési változások például az adatforrás frissítésének egyike. Itt látható a gyorsítótárat érvénytelenítő módosítások teljes listája:

* Váltás az adatforrás típusára
* Váltás az adatforrás-tárolóra
* Adatforráshoz tartozó hitelesítő adatok
* Adatforrás-változás észlelési házirendje
* Adatforrások törlésének észlelési szabályzata
* Indexelő mező-hozzárendelések
* Indexelő paraméterei
    * Elemzési mód
    * Kizárt fájlnévkiterjesztések
    * Indexelt fájlnévkiterjesztések
    * A tárolási metaadatok indexelése csak a túlméretezett dokumentumok esetében
    * Tagolt szöveges fejlécek
    * Tagolt szöveg elválasztója
    * Dokumentum gyökere
    * Kép művelet (a képek kibontásának módosításai)

### <a name="changes-that-trigger-incremental-processing"></a>Növekményes feldolgozást kiváltó változások

A növekményes feldolgozás kiértékeli a készségkészlet-definícióját, és meghatározza, hogy mely készségeket kell újra futtatni, szelektíven frissíteni a dokumentum fájának érintett részeit. Itt látható a növekményes bővítést eredményező változások teljes listája:

* A készségkészlet lévő szakértelem típusa eltérő. A szakértelem OData-típusa frissítve
* A szaktudás-specifikus paraméterek frissítve lettek, például az URL-cím, az alapértelmezett beállítások vagy más paraméterek
* A szaktudás kimenetei megváltoznak, a szakértelem további vagy eltérő kimeneteket ad vissza.
* A szaktudás frissítései eltérő ősei, a szaktudás láncolása megváltozott, azaz szaktudás bemenetei
* A felsőbb rétegbeli képzettségek nem érvényesek, ha az ehhez a szaktudáshoz bemenetet biztosító képesség frissül
* A Knowledge Store-leképezési hely frissítései, a dokumentumok újravetítésének eredményei
* A Knowledge Store-kivetítések módosításai, a dokumentumok újravetítésének eredményei
* A kimeneti mezők leképezése módosult egy indexelő esetében, így a dokumentumok újravetítése az indexbe

## <a name="api-reference"></a>API-referencia

A REST API `2019-05-06-Preview` verziója az indexelő, a szakértelmével és az adatforrások további tulajdonságai révén növekményes dúsítást biztosít. A dokumentáción kívül az API-k meghívásával kapcsolatos részletekért lásd: a [gyorsítótárazás konfigurálása a növekményes](search-howto-incremental-index.md) bővítéshez.

+ [Indexelő létrehozása (API-Version = 2019-05 -06 – előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/create-indexer) 

+ [Indexer frissítése (API-Version = 2019-05 -06 – előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-indexer) 

+ [Készségkészlet frissítése (API-Version = 2019-05 -06 – előzetes verzió)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/update-skillset) (új URI-paraméter a kérésen)

+ [Reset Skills (api-version=2019-05-06-Preview)](https://docs.microsoft.com/rest/api/searchservice/2019-05-06-preview/reset-skills)

+ Adatbázis-indexelő (Azure SQL, Cosmos DB). Egyes indexelő lekérdezéseken keresztül kérik le az adatforrásokat. Az Adatlekérdezési lekérdezések esetében az adatforrás [frissítése](https://docs.microsoft.com/rest/api/searchservice/update-data-source) egy új paramétert támogat egy kérelem **ignoreResetRequirement**, amelyet akkor kell beállítani, `true` ha a frissítési művelet nem érvényteleníti a gyorsítótárat. 

  A **ignoreResetRequirement** takarékosan használható, mert nem kívánt inkonzisztenciát eredményezhet az adataiban, amelyeket nem lehet könnyen észlelni.

## <a name="next-steps"></a>További lépések

A növekményes bővítés egy hatékony szolgáltatás, amely kibővíti a változások követését a szakértelmével és a mesterséges intelligenciával. A AIncremental-bővítés lehetővé teszi a meglévő feldolgozott tartalom újbóli használatát a készségkészlet-kialakítás megismétlése során.

A következő lépésként engedélyezze a gyorsítótárazást egy meglévő indexelő esetében, vagy adjon hozzá egy gyorsítótárat egy új indexelő definiálásához.

> [!div class="nextstepaction"]
> [A növekményes dúsítás gyorsítótárazásának konfigurálása](search-howto-incremental-index.md)
