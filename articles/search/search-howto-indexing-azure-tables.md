---
title: "Az Azure Search Azure Table storage indexelése |} Microsoft Docs"
description: "Útmutató: az Azure Search Azure Table storage-ban tárolt adatok index"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 04/10/2017
ms.author: eugenesh
ms.openlocfilehash: b167f69f853f6ecdfd56179e6ffb946cdf2f45b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="index-azure-table-storage-with-azure-search"></a>Az Azure Search index Azure Table storage
Ez a cikk bemutatja, hogyan használható az Azure Search Azure Table storage-ban tárolt adatok indexeléséhez.

## <a name="set-up-azure-table-storage-indexing"></a>Azure Table storage indexelése beállítása

Az Azure Table storage indexelő állíthat be ezeket az erőforrásokat használatával:

* [Azure Portal](https://ms.portal.azure.com)
* Az Azure Search [REST API-n](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Az Azure Search [.NET SDK-val](https://aka.ms/search-sdk)

Itt bemutatjuk, a folyamat a REST API használatával. 

### <a name="step-1-create-a-datasource"></a>1. lépés: Egy adatforrás létrehozása

Egy adatforrás határozza meg, hogy mely adatok indexelését, az adatokat, és a házirendeket, amelyek lehetővé teszik az adatok változásai hatékonyan azonosításához az Azure Search eléréséhez szükséges hitelesítő adatokat.

Tábla indexelő, az adatforrás rendelkeznie kell a következő tulajdonságokkal:

- **név** az adatforrást a keresőszolgáltatása belül egyedi neve.
- **típus** kell `azuretable`.
- **hitelesítő adatok** a paraméter tartalmazza a tárolási fiók kapcsolati karakterlánc. Tekintse meg a [adja meg a hitelesítő adatok](#Credentials) című szakaszban talál információt.
- **tároló** állítja be a táblázat nevét és opcionális lekérdezés.
    - Adja meg a táblanevet használatával a `name` paraméter.
    - Megadhat egy lekérdezés segítségével a `query` paraméter. 

> [!IMPORTANT] 
> Amikor csak lehetséges, használjon szűrőt PartitionKey jobb teljesítmény érdekében. Bármely más lekérdezés nincs teljesítményproblémákat nagy táblák egy teljes tábla ellenőrzése. Tekintse meg a [teljesítménnyel kapcsolatos szempontok](#Performance) szakasz.


Adatforrás létrehozása:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
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

A hitelesítő adatokat megadhatja a következő táblázatban ezen módszerek valamelyikével: 

- **Teljes hozzáférés tárolási fiók kapcsolati karakterlánc**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` letölthető a kapcsolati karakterláncot az Azure-portálon a a **Storage-fiók panelen** > **beállítások** > **kulcsok** (a klasszikus tárfiókokba) vagy **beállítások** > **hívóbetűk** (az Azure Resource Manager storage-fiókok).
- **A tárfiók megosztott hozzáférési aláírást kapcsolati karakterlánc**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` a közös hozzáférésű jogosultságkódot van a listája, és meg kell-e olvasási engedéllyel a (jelen esetben táblák) tárolók és objektumok (táblázat sorait).
-  **Tábla közös hozzáférésű jogosultságkódot**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` a közös hozzáférésű jogosultságkódot (olvasás) lekérdezés engedélyekkel kell rendelkezniük a táblában.

További információ a megosztott tárolón aláírások hozzáférni, lásd: [használata közös hozzáférésű jogosultságkód](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Ha megosztott hozzáférési aláírást hitelesítő adatok használatához szüksége lesz az adatforrás hitelesítő adatainak frissítése rendszeresen megújított aláírásokkal, hogy megakadályozza a lejárati idejük. Ha megosztott hozzáférési aláírást hitelesítő adatok lejárnak, az indexelő futása sikertelen, és hasonló "A kapcsolódási karakterláncban megadott hitelesítő adatok érvénytelenek, vagy lejárt." hibaüzenet  

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása
Az index egy dokumentum, az attribútumok megadja azokat a mezőket, és más, a keresés alakul szerkezetek tapasztalnak.

Index létrehozása:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
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

### <a name="step-3-create-an-indexer"></a>3. lépés:, Hozzon létre egy indexelőt
Az indexelő datasource csatlakoztatja a cél keresési indexszel rendelkező és automatizálhatja az adatfrissítési ütemezés biztosít. 

Az index és az adatforrás létrehozása után készen áll az indexelő létrehozásához:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Az indexelő futása két óránként. (Az ütemezési időköznek "PT2H" tulajdonság értéke.) Az indexelő 30 percenként "PT30M" intervallum be. A legrövidebb támogatott időköz értéke öt perc. Az ütemezés nem kötelező megadni. Ha nincs megadva, az indexelő futása, csak egyszer, amikor létrejön. Azonban az indexelő bármikor futtathatja.   

Hozzon létre indexelő API további információkért lásd: [létrehozása indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

## <a name="deal-with-different-field-names"></a>Különböző mezőnevek kezelése
Egyes esetekben a mezőnevek a meglévő index eltérnek a a tulajdonságnevek a táblában. Mező leképezéseit segítségével a mezők nevét a táblázatból a tulajdonságneveket hozzárendelését a keresési index. Mező hozzárendelések kapcsolatos további információkért lásd: [Azure keresési indexelő mező hozzárendelések híd adatforrások és a keresési indexek közötti különbségek](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>A dokumentum kulcsok kezelése
Az Azure Search a dokumentum kulcs egyedileg azonosít egy dokumentumot. Minden keresési index rendelkeznie kell típusú pontosan egy kulcsmező `Edm.String`. A kulcs mező kitöltése kötelező az indexhez hozzáadott minden dokumentumhoz. (Ez valójában a csak kötelezően kitöltendő mezőben.)

Tábla sora egy összetett kulcs, mert az Azure Search hoz létre egy szintetikus nevű mező `Key` , amely partíciós kulcs és a sor kulcsértékei összefűzése. Például ha egy sor PartitionKey van `PK1` és RowKey `RK1`, akkor a `Key` mező értéke `PK1RK1`.

> [!NOTE]
> A `Key` érték dokumentum kulcsok, például kötőjelek érvénytelen karaktert tartalmaz. Akkor is foglalkozik érvénytelen karakterek használatával a `base64Encode` [leképezési függvény mezőben](search-indexer-field-mappings.md#base64EncodeFunction). Ha így tesz, akkor ne felejtse el is használja a biztonságos URL-cím Base64 kódolást, amikor például a keresési dokumentum kulcsok benyújtása API meghívja.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Növekményes indexelő és törlési észlelése
Egy tábla indexelő ütemezés szerint futtatni beállításakor azt reindexes csak új vagy frissített sorok, egy sor alapján `Timestamp` érték. Nem kell megadnia a módosítás szabályzat. Növekményes indexelő engedélyezve van, automatikusan.

Azt jelzi, hogy bizonyos dokumentumok el kell távolítani az indexből, egy helyreállítható törlésre stratégia is használhat. Helyett a sor törlése, annak jelzésére, hogy azt rendelkezik törölve, és állítson be egy helyreállítható törlési szabályzat a adatforráson tulajdonság hozzáadása. Például a következő házirendet úgy véli, hogy egy sor törölve, ha a sor tulajdonsággal rendelkezik `IsDeleted` értékű `"true"`:

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
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

Alapértelmezés szerint az Azure Search használja a következő lekérdezés szűrője: `Timestamp >= HighWaterMarkValue`. Azure-táblákban nem rendelkezik egy másodlagos index a a `Timestamp` mezőjét, ez egy teljes tábla vizsgálatot igényel, és ezért nagy táblák lassú.


Az alábbiakban az indexelési teljesítmény tábla két lehetséges módszer. Mindkét megoldás támaszkodnak az táblázat partíciókat: 

- Ha az adatok természetes lehet particionálni be több partíció-címtartományokat, hozzon létre egy adatforrást és egy megfelelő indexelő minden partíció tartományra. Minden egyes indexelő most már csak egy adott partícióra címtartományt, lekérdezés jobb teljesítményt eredményez feldolgozni. Ha az adatok indexelése igénylő rögzített partíciók, még élvezetesebbé kis számú: minden indexelő csak egy partíció vizsgálat does. Ahhoz például, hogy hozzon létre egy adatforrást egy partíciótartomány feldolgozásának származó kulccsal rendelkező `000` való `100`, ilyen lekérdezés használata: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Ha az adatok particionálása idő szerint (például létrehozhat egy új partíció minden nap vagy hét), vegye figyelembe a következő módon: 
    - Az űrlap lekérdezéssel: `(PartitionKey ge <TimeStamp>) and (other filters)`. 
    - Figyelje az indexelő folyamatban [indexelő állapot API beszerzése](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status), és rendszeres időközönként frissíti a `<TimeStamp>` feltétel alapján a legutóbbi magas vízjel érték sikeres lekérdezés. 
    - Ezt a módszert Ha szeretné elindítani a teljes újraindexelés, akkor alaphelyzetbe kell a datasource lekérdezés mellett az indexelő alaphelyzetbe állítása. 


## <a name="help-us-make-azure-search-better"></a>Segítsen az Azure Search továbbfejlesztésében
Ha funkciókra vonatkozó kérések vagy ötleteket javításai, elküldeni őket a [UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search/).
