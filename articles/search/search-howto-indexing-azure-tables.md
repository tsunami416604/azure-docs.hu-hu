---
title: Tartalom indexelése az Azure Table storage, az Azure Search – a teljes szöveges keresés
description: Ismerje meg az Azure Search indexelők az Azure Table storage-ban tárolt adatok indexelése.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: e1b411ab54a5b666849893ba9d246eff85e7e54e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306884"
---
# <a name="index-azure-table-storage-with-azure-search"></a>Az Azure Search szolgáltatással az Azure Table storage indexelése
Ez a cikk bemutatja, hogyan használhatja az Azure Search index, az Azure Table storage-ban tárolt adatok.

## <a name="set-up-azure-table-storage-indexing"></a>Állítsa be az Azure Table storage-indexelő

Az Azure Table storage-indexelő beállítása a forrásanyagok használata:

* [Azure Portal](https://ms.portal.azure.com)
* Az Azure Search [REST API-val](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Search [.NET SDK](https://aka.ms/search-sdk)

Itt bemutatjuk a folyamatot a REST API-val. 

### <a name="step-1-create-a-datasource"></a>1. lépés: Adatforrás létrehozása

Egy adatforrás indexelni, a az adatokat, és a házirendeket, amelyek lehetővé teszik az Azure Search hatékony azonosításához az adatok módosítása eléréséhez szükséges hitelesítő adatok megadása

A tábla indexelése, az adatforrást a következő tulajdonságokkal kell rendelkeznie:

- **név** az adatforrást a keresési szolgáltatás belül egyedi neve.
- **típus** kell `azuretable`.
- **hitelesítő adatok** paraméter a tárfiók kapcsolati sztringje tartalmazza. Tekintse meg a [adja meg a hitelesítő adatok](#Credentials) című szakasz részletezi.
- **tároló** állítja be a táblázat neve és a egy opcionális lekérdezési.
    - A táblázat neve használatával adja meg a `name` paraméter.
    - Szükség esetén adjon meg egy lekérdezést a `query` paraméter. 

> [!IMPORTANT] 
> Amikor csak lehetséges, használjon szűrőt a partitionkey értékéhez a jobb teljesítmény érdekében. Minden más lekérdezés a teljes tábla beolvasásával, ami nagy táblák esetében teljesítményromlást végzi. Tekintse meg a [teljesítménnyel kapcsolatos megfontolások](#Performance) szakaszban.


Egy adatforrás létrehozása:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Adatforrás létrehozása API további információkért lásd: [adatforrás létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>Hitelesítő adatok megadása ####

A hitelesítő adatokat megadhatja a tábla a következő módszerek egyikével: 

- **Teljes hozzáférés tárfiók kapcsolati sztringje**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Kérheti a kapcsolati karakterláncot az Azure Portalról nyissa meg a **Tárfiók panelén** > **beállítások** > **kulcsok** (a klasszikus üzemi modell esetén Storage-fiókok esetében) vagy **beállítások** > **hozzáférési kulcsok** (az Azure Resource Manager-tárfiókok).
- **Tárfiók megosztott hozzáférési aláírást kapcsolati karakterlánc**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` A közös hozzáférésű jogosultságkód kell rendelkeznie a listában, és olvasási jogosultságokkal (ebben az esetben táblák) tárolókkal és objektumokkal (táblázat sorai).
-  **Tábla közös hozzáférésű jogosultságkód**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` A közös hozzáférésű jogosultságkód lekérdezés (olvasás) engedélyekkel kell rendelkeznie a tábla.

További információ a megosztott tároló eléréséhez aláírásokat, lásd: [a közös hozzáférésű jogosultságkódot](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Ha a közös hozzáférésű jogosultságkód hitelesítő adatokat használja, szüksége lesz az adatforrás hitelesítő adatainak frissítése rendszeresen megújított jogosultságkódokkal lejárati idejük elkerülése érdekében. Ha a közös hozzáférésű jogosultságkód hitelesítő adatai lejárnak, az indexelő hibaüzenettel meghiúsul egy hasonló "A kapcsolati karakterláncban megadott hitelesítő adatok érvénytelenek, vagy lejárt."  

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása
Az index egy dokumentumot, az attribútumot, adja meg a mezőket, és egyéb szerkezetek, amelyek formázhatja a keresési élmény.

Index létrehozása:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Indexek létrehozásával kapcsolatos további információkért lásd: [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>3. lépés: Indexelő létrehozása
Az indexelő a cél keresési indexhez datasource csatlakozik, és biztosítja az Adatfrissítés automatizálásához ütemezés szerint. 

Az index és az adatforrás létrehozása után készen áll az indexelő létrehozása:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Az indexelő futása kétóránként. (Az ütemezési időköz értéke "PT2H".) Az indexelők futtatásához a 30 percenként, a "PT30M" időközt beállítani. A legrövidebb támogatott időköz öt perc alatt. Nem kötelező megadni. az ütemezés Ha nincs megadva, az indexelők futtatja, csak ha a létrehozást követően. Ugyanakkor igény szerint bármikor az indexelő is futtathatja.   

Indexelő létrehozása API további információkért lásd: [indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Különböző mezőnevek kezelése
Egyes esetekben a meglévő index a mezőnevek eltérnek a tulajdonságneveket a táblában. Mezőleképezések használhatja a mezők nevét a táblázatból tulajdonságneveket leképezni a keresési index. További információ a mezőmegfeleltetésről, lásd: [Azure Search-indexelő Mezőleképezések áthidalhatók az adatforrások és a keresési indexek közötti különbségekről](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>A dokumentum kulcsok kezelése
Az Azure Search szolgáltatásban a dokumentumkulcsot egyedileg azonosítja az egy dokumentumot. Minden keresési index rendelkeznie kell típusú pontosan egy kulcsmező `Edm.String`. Kulcsmező szükség minden egyes dokumentum, amely az indexet ad hozzá. (Tulajdonképpen azt az egyetlen mező kitöltése kötelező.)

Táblázatsorok egy összetett kulccsal rendelkezik, mert az Azure Search állít elő, szintetikus mezője `Key` , amely partíciós kulcs és a sor értékek összefűzésével. Például ha egy sort a PartitionKey, `PK1` rowkey tulajdonságok esetén pedig `RK1`, akkor a `Key` mezők értékei `PK1RK1`.

> [!NOTE]
> A `Key` érték a dokumentum kulcsok, például kötőjelek érvénytelen karaktert tartalmaz. Az érvénytelen karakterek kezelhető a `base64Encode` [leképezési függvény mezőben](search-indexer-field-mappings.md#base64EncodeFunction). Ha így tesz, ne felejtse el is használhatja az URL-cím környezetben is biztonságos Base64 kódolást, amikor például a keresési meghívja passing dokumentum kulcsok API-ban.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Növekményes indexelést és a törlés észlelése
Egy táblában indexelője ütemezés szerint futtatni beállításakor azt reindexes csak az új vagy frissített sorok határoz meg egy sor `Timestamp` értéket. Adja meg a változásészlelési házirend nem kell. Indexelő növekményes automatikusan engedélyezve lesz az Ön számára.

Jelzi, hogy bizonyos dokumentumok el kell távolítani az indexet, használhatja a helyreállítható törlés stratégiát. Helyett egy sor törlése, adjon hozzá egy tulajdonság jelzi, hogy azt törölni, pedig az az adatforrás egy helyreállítható törlési szabályzat beállítása. Például a következő szabályzatot úgy véli, hogy egy sor törlődött, ha a sor nullértékkel rendelkezik egy tulajdonság `IsDeleted` értékkel `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Alapértelmezés szerint az Azure Search használja a következő lekérdezési szűrő: `Timestamp >= HighWaterMarkValue`. Mivel az Azure-táblák nem rendelkezik egy másodlagos indexet a `Timestamp` mezőt, ilyen típusú lekérdezésekre van szükség a teljes tábla beolvasásával, és ezért lassú nagy táblák esetében.


Az alábbiakban két lehetséges módszer tábla indexelési teljesítmény javításához. Mindkét módszer táblapartíciók használatára támaszkodik: 

- Ha az adatok természetes módon kell felosztani több partíció-címtartományokat, hozzon létre egy adatforrás- és egy megfelelő egy-egy partíció tartományhoz. Minden indexelő most már csak egy adott partícióra tartomány, jobb lekérdezési teljesítményt eredményez feldolgozásához. Ha az adatok indexelése rögzített partíciók, még jobb kis számú: minden indexelő csak egy partíció vizsgálat does. Ha például egy adatforrás egy partíciótartomány feldolgozásra a kulcsok létrehozásához `000` való `100`, ehhez hasonló lekérdezés használata: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Ha az adatok particionálása idő szerint (például, hogy új partíciót létrehozni minden nap vagy hét), vegye figyelembe a következő módon: 
    - Használja a lekérdezés a következő formában: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Az indexelő állapotának figyelése [indexelő állapotának API első](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status), és rendszeres időközönként frissíti a `<TimeStamp>` feltétel alapján a legutóbbi magas vízjel értéket a sikeres lekérdezés. 
    - Ezt a módszert használja Ha szeretné elindítani a teljes újraindexelés, akkor alaphelyzetbe kell állítania az adatforrás lekérdezési mellett az indexelő alaphelyzetbe állítása. 


## <a name="help-us-make-azure-search-better"></a>Segítsen jobbá Azure Search
Ha funkcióra vonatkozó javaslata vagy ötlete van javításai, küldje el őket a saját [UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search/).
