---
title: Bevezetés a növekményes indexelésbe (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: Állítsa be az AI-bővítési folyamatot úgy, hogy az adatait az esetleges konzisztencia érdekében a szaktudás, szakértelmével, indexelő vagy adatforrások frissítéseinek kezeléséhez irányítsa.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ea3bcfc25040f09b6871d85412ac64061ec2f9e8
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549115"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Mi az Azure Cognitive Search növekményes indexelése?

> [!Note]
> A növekményes indexelés előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nem érhető el portál vagy .NET SDK-támogatás.
>

A növekményes indexelés az Azure Cognitive Search új funkciója, amely lehetővé teszi a gyorsítótárazást és az állapotot egy kognitív készségkészlet, amely a gazdagított tartalomhoz tartozó, az egyes lépések feldolgozására és újrafeldolgozására ad lehetőséget. Ez nem csak a pénzügyi beruházásokat tartja fenn, hanem hatékonyabb rendszerre is. Ha a rendszer gyorsítótárazza a struktúrákat és a tartalmakat, az indexelő meghatározhatja, hogy mely készségek módosultak és fussanak, és csak azokat, amelyeket módosítottak, valamint az alsóbb rétegbeli függő képességeket is. 

A növekményes indexeléssel a alkoholtartalom-növelési folyamat jelenlegi verziója a lehető legkevesebb munkamennyiséget biztosítja az indexben lévő összes dokumentum konzisztenciájának biztosításához. Olyan helyzetekben, ahol teljes hozzáférést szeretne használni, részletes vezérlőket használhat a várt viselkedés felülbírálásához. A konfigurálással kapcsolatos további információkért lásd: [növekményes indexelés beállítása](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Indexelő gyorsítótár

A növekményes indexelés hozzáadja az indexelő gyorsítótárat a dúsítási folyamathoz. Az indexelő gyorsítótárba helyezi az eredményeket a dokumentum repedése és az egyes dokumentumok egyes képességeinek kimenetei alapján. A készségkészlet frissítésekor a rendszer csak a megváltozott vagy az alárendelt képességeket futtatja újra. A frissített eredményeket a rendszer a gyorsítótárba írja, és a dokumentum frissül az indexben és a Knowledge Store-ban.

Fizikailag a gyorsítótár egy Storage-fiók. A keresési szolgáltatáson belüli összes index ugyanazt a Storage-fiókot is megoszthatja az indexelő gyorsítótárban. Minden indexelő egyedi és nem módosítható gyorsítótár-azonosítót kap.

### <a name="cache-configuration"></a>Gyorsítótár-konfiguráció

A növekményes indexelésből való kihasználás megkezdéséhez be kell állítania a `cache` tulajdonságot az indexelő alkalmazásban. Az alábbi példa egy olyan indexelő mutat be, amelyen engedélyezve van a gyorsítótárazás. A konfiguráció adott részeit a következő szakasz ismerteti.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters":{}
}
```

Ha ezt a tulajdonságot első alkalommal állítja be egy meglévő indexelő alkalmazásban, akkor azt is vissza kell állítania, ami az adatforrásban lévő összes dokumentumot újra feldolgozza. A növekményes indexelés célja, hogy az index dokumentumai konzisztensek legyenek az adatforrással és a készségkészlet aktuális verziójával. Az index alaphelyzetbe állítása az első lépés a konzisztencia irányába, mivel a készségkészlet korábbi verziói által dúsított dokumentumokat nem távolítja el. Az indexelő alaphelyzetbe kell állítani, hogy konzisztens alaptervet hozzon létre.

### <a name="cache-lifecycle"></a>Gyorsítótár életciklusa

A gyorsítótár életciklusát az indexelő kezeli. Ha az indexelő `cache` tulajdonsága NULL értékűre van állítva, vagy a kapcsolódási sztring módosult, a rendszer törli a meglévő gyorsítótárat. A gyorsítótár életciklusa az indexelő életciklushoz is kötődik. Ha töröl egy indexelő, a társított gyorsítótár is törlődik.

### <a name="indexer-cache-mode"></a>Indexelő gyorsítótáras üzemmód

Az indexelő gyorsítótára olyan módokon működhet, amelyekben a rendszer csak a gyorsítótárba írja az adatírást, vagy az adatlemezeket a gyorsítótárba írja, és a dokumentumok újbóli gazdagítása céljából használja.  Ideiglenesen felfüggesztheti a növekményes bővítést úgy, hogy a gyorsítótárban lévő `enableReprocessing` tulajdonságot `false`re állítja, és később a növekményes bővítést is folytatja, és a végső konzisztencia-szabályozást úgy állítja be, hogy `true`. Ez a vezérlő különösen akkor hasznos, ha rangsorolni szeretné az új dokumentumok indexelését, így biztosítva a teljes körű dokumentumok közötti konzisztenciát.

## <a name="change-detection-override"></a>Változás-észlelés felülbírálása

A növekményes indexelés részletesen szabályozza a dúsítási folyamat összes aspektusát. Ez a vezérlő lehetővé teszi olyan helyzetek kezelését, amelyekben a változás nem szándékolt következményekkel járhat. A készségkészlet szerkesztése és az egyéni képességek URL-címének frissítése például azt eredményezi, hogy az indexelő érvényteleníti az adott képességhez tartozó gyorsítótárazott eredményeket. Ha csak egy másik virtuális gépre helyezi át a végpontot, vagy egy új hozzáférési kulccsal újra üzembe helyezi a képességet, valójában nem szeretné, hogy a meglévő dokumentumok újra fel legyenek dolgozva.

Annak biztosítása érdekében, hogy az indexelő csak a kifejezetten megkövetelt bővítéseket adja meg, a készségkészlet frissítései opcionálisan a `disableCacheReprocessingChangeDetection` querystring paramétert is megadhatják `true`. Ha be van állítva, ez a paraméter biztosítja, hogy csak a készségkészlet frissítései legyenek véglegesítve, és a változás nem lesz kiértékelve a meglévő Corpus hatására.

Az alábbi példa a querystring használatát szemlélteti. A kérelem része, & tagolt kulcs érték párokkal. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Gyorsítótár-érvénytelenítés

Ennek a forgatókönyvnek az a célja, hogy az egyéni képességek új verziójának bevezetése ne a dúsítási folyamat változásain belül történjen, de egy adott szakértelmet érvényteleníteni kell, és az összes érintett dokumentum újrafeldolgozásra kerül, hogy tükrözze a frissített modell előnyeit. Ilyen esetekben meghívhatja a készségkészlet invalidate műveletet. A képességek alaphelyzetbe állítása API elfogad egy POST-kérést a gyorsítótárban, amelyet érvényteleníteni kell. További információ a képességek alaphelyzetbe állítása API-ról: [Indexelő alaphelyzetbe állítása (Search REST API)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="bi-directional-change-detection"></a>Kétirányú változás észlelése

Az indexelő nem csak az új dokumentumokat helyezi át és dolgozza fel, de most már visszafelé helyezheti őket, és a korábban feldolgozott dokumentumokat a konzisztencia felé irányíthatja. Ezzel az új képességgel fontos tisztában lenni azzal, hogy a dúsítási folyamat összetevőinek változásai hogyan eredményezik az indexelő működését. Az indexelő várólistára kerül, ha olyan változást azonosít, amely érvénytelenítve vagy inkonzisztens a gyorsítótárazott tartalomhoz képest.

### <a name="invalidating-changes"></a>Módosítások érvénytelenítése

A módosítások érvénytelenítése ritkán fordul elő, de jelentős hatással van a dúsítási folyamat állapotára. Az érvénytelenítési változás egy olyan esetben, amikor a teljes gyorsítótár már nem érvényes. Az érvénytelenítési változások például az adatforrás frissítésének egyike. Ha tudja, hogy a módosítás ne érvénytelenítse a gyorsítótárat, például a kulcsot a Storage-fiókban, a `ignoreResetRequirement` querystring paramétert úgy kell beállítani, hogy az adott erőforrás frissítési művelete `true` legyen, így biztosítva, hogy a művelet nem elutasítva.

Itt látható a gyorsítótárat érvénytelenítő módosítások teljes listája:

* Váltás az adatforrás típusára
* Váltás az adatforrás-tárolóra
* Adatforrás hitelesítő adatai
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

### <a name="inconsistent-changes"></a>Inkonzisztens változások

A inkonzisztens változások például a készségkészlet frissítése, amely egy képességet módosít. A módosítás a gyorsítótár egy részét inkonzisztensvé teheti. Az indexelő azonosítja azt a munkát, hogy a dolgok konzisztensek legyenek.  

A gyorsítótár inkonzisztenciát eredményező változásainak teljes listája:

* A készségkészlet lévő szakértelem típusa eltérő. A szakértelem OData-típusa frissítve
* A szaktudás-specifikus paraméterek frissítve lettek, például az URL-cím, az alapértelmezett beállítások vagy más paraméterek
* A szaktudás kimenetei megváltoznak, a szakértelem további vagy eltérő kimeneteket ad vissza.
* A szaktudás frissítései eltérő ősei, a szaktudás láncolása megváltozott, azaz Szaktudás bemenetei
* A felsőbb rétegbeli képzettségek nem érvényesek, ha az ehhez a szaktudáshoz bemenetet biztosító képesség frissül
* A Knowledge Store-leképezési hely frissítései, a dokumentumok újravetítésének eredményei
* A Knowledge Store-kivetítések módosításai, a dokumentumok újravetítésének eredményei
* A kimeneti mezők leképezése módosult egy indexelő esetében, így a dokumentumok újravetítése az indexbe

## <a name="rest-api-reference-for-incremental-indexing"></a>A növekményes indexelés REST API referenciája

A REST `api-version=2019-05-06-Preview` biztosítja az API-kat a növekményes indexeléshez, az indexelő, a szakértelmével és az adatforrások hozzáadásával. A hivatkozási dokumentáció jelenleg nem tartalmazza ezeket a kiegészítéseket. A következő szakasz az API-változásokat ismerteti.

### <a name="indexers"></a>Indexelők

Az [Indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer) és az [Indexelő](https://docs.microsoft.com/rest/api/searchservice/update-indexer) mostantól elérhetővé teszi a gyorsítótárral kapcsolatos új tulajdonságokat:

* `StorageAccountConnectionString`: a köztes eredmények gyorsítótárazásához használt Storage-fiókhoz tartozó kapcsolódási karakterlánc.

* `CacheId`: a `cacheId` a `annotationCache` Storage-fiókban lévő tároló azonosítója, amelyet a rendszer gyorsítótárként fog használni az indexelő számára. Ez a gyorsítótár egyedi lesz az indexelő számára, és ha az indexelő törölve lett, és ugyanazzal a névvel lett létrehozva, akkor a `cacheId` újból létrejön. A `cacheId` nem állítható be, mindig a szolgáltatás hozza létre.

* `EnableReprocessing`: alapértelmezés szerint `true`re van állítva, ha a `false`értékre van állítva, a dokumentumok továbbra is a gyorsítótárba lesznek írva, de a gyorsítótár-beállítások alapján a rendszer nem dolgozza fel újra a meglévő dokumentumokat.

Egyes indexelő ( [adatforrásokon](https://docs.microsoft.com/rest/api/searchservice/create-data-source)keresztül) lekérik az adatlekérdezéseket. Az olyan lekérdezések esetében, amelyek az adatlekérdezéseket kérik, az indexek egy új lekérdezési karakterlánc paramétert is támogatnak: `ignoreResetRequirement` `true` kell beállítani, ha a frissítési művelet nem érvényteleníti a gyorsítótárat.

### <a name="skillsets"></a>Készségek

A szakértelmével nem támogatja az új műveleteket, de támogatni fogja az új querystring paramétert: a `disableCacheReprocessingChangeDetection`t úgy kell beállítani, hogy `true`, ha a jelenlegi művelet alapján nem kívánja frissíteni a meglévő dokumentumokat.

### <a name="datasources"></a>adatforrások

Az adatforrások nem támogatják az új műveleteket, de támogatják az új querystring paramétert: a `ignoreResetRequirement` `true` kell beállítani, ha a frissítési művelet nem érvényteleníti a gyorsítótárat.

## <a name="best-practices"></a>Ajánlott eljárások

A növekményes indexelés használatának ajánlott módszere a növekményes indexelés konfigurálása egy új indexelő gyorsítótár-tulajdonságának beállításával vagy egy meglévő indexelő alaphelyzetbe állításával és a gyorsítótár tulajdonság beállításával.

Az `ignoreResetRequirement`t takarékosan használhatja, mivel az olyan nem kívánt inkonzisztenciát eredményezhet az adataiban, amelyeket nem lehet könnyen észlelni.

## <a name="takeaways"></a>Legfontosabb ismeretek

A növekményes indexelés egy hatékony szolgáltatás, amely kibővíti a változások nyomon követését az adatforrásból a dúsítási folyamat valamennyi aspektusára, beleértve az adatforrást, a készségkészlet aktuális verzióját és az indexelő. A szaktudás, a szakértelmével vagy a gazdagítás fejlődése során a dúsítási folyamat biztosítja a lehető legkisebb munkát, miközben továbbra is a dokumentumokat a végleges konzisztencia érdekében hajtja végre.

## <a name="next-steps"></a>További lépések

Ismerkedjen meg a növekményes indexeléssel egy meglévő indexelő gyorsítótárának hozzáadásával, vagy adja hozzá a gyorsítótárat egy új indexelő definiálásához.

> [!div class="nextstepaction"]
> [Növekményes indexelés beállítása](search-howto-incremental-index.md)
