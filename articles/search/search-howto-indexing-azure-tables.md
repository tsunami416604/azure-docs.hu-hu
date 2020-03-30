---
title: Keresés az Azure Table storage-tartalmában
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan indexelhet adatokat az Azure Table storage egy Azure Cognitive Search indexelő.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e8f6c0454497b1cb1d62417e566e9662469c56d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112997"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Táblák indexelése az Azure Table storage-ból az Azure Cognitive Search segítségével

Ez a cikk bemutatja, hogyan használhatja az Azure Cognitive Search az Azure Table storage-ban tárolt adatok indexeléséhez.

## <a name="set-up-azure-table-storage-indexing"></a>Az Azure Table storage indexelése

Az Azure Table storage indexelő az alábbi erőforrások használatával állítható be:

* [Azure-portál](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](https://aka.ms/search-sdk)

Itt bemutatjuk a folyamatot a REST API használatával. 

### <a name="step-1-create-a-datasource"></a>1. lépés: Adatforrás létrehozása

Az adatforrás határozza meg, hogy mely adatokat indexelni, az adatok eléréséhez szükséges hitelesítő adatokat, és a szabályzatok, amelyek lehetővé teszik az Azure Cognitive Search az adatok változásainak hatékony azonosításához.

A táblaindexeléshez az adatforrásnak a következő tulajdonságokkal kell rendelkeznie:

- **a név** a keresési szolgáltatáson belüli adatforrás egyedi neve.
- **a típusnak** kell lennie. `azuretable`
- **hitelesítő adatok** paraméter tartalmazza a tárfiók kapcsolati karakterláncát. A részleteket a [Hitelesítő adatok megadása](#Credentials) című szakaszban találja.
- **tároló** beállítja a tábla nevét és egy választható lekérdezést.
    - Adja meg a tábla `name` nevét a paraméter használatával.
    - Szükség esetén adjon meg egy `query` lekérdezést a paraméter használatával. 

> [!IMPORTANT] 
> Amikor csak lehetséges, használjon szűrőt partitionkey a jobb teljesítmény érdekében. Bármely más lekérdezés teljes táblavizsgálatra készül, ami a nagy táblák gyenge teljesítményét eredményezi. Tekintse meg a Teljesítményekkel kapcsolatos szempontok című [szakaszt.](#Performance)


Adatforrás létrehozása:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

Az Adatforrás létrehozása API-ról további információt az [Adatforrás létrehozása című](https://docs.microsoft.com/rest/api/searchservice/create-data-source)témakörben talál.

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>A hitelesítő adatok megadásának módjai ####

A táblázat hitelesítő adatait az alábbi módokon adja meg: 

- **Teljes hozzáférésű tárfiók-kapcsolati karakterlánc:** `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` A kapcsolati karakterlánc az Azure Portalról a **Storage-fiók** > **beállítási** > **kulcsai** (a klasszikus tárfiókok) vagy a **Settings** > Access**kulcsok** (az Azure Resource Manager storage-fiókok) megkeresi a kapcsolati karakterláncot.
- **A tárfiók megosztott hozzáférési jogosultsági jogosultsági karakterlánca**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` A megosztott hozzáférésű aláírásnak rendelkeznie kell a tárolók (ebben az esetben táblák) és az objektumok (táblasorok) listájával és olvasási engedélyével.
-  **Közös hozzáférésű tábla:** `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` A megosztott hozzáférésű aláírásnak lekérdezési (olvasási) engedélyekkel kell rendelkeznie a táblában.

A megosztott tárolási hozzáférésű aláírásokról a [Megosztott hozzáférésű aláírások használata című témakörben](../storage/common/storage-dotnet-shared-access-signature-part-1.md)talál további információt.

> [!NOTE]
> Ha megosztott hozzáférésű aláírási hitelesítő adatokat használ, rendszeresen frissítenie kell az adatforrás hitelesítő adatait a megújult aláírásokkal, hogy megakadályozza azok lejáratát. Ha a megosztott hozzáférésű aláírás hitelesítő adatai lejárnak, az indexelő a "A kapcsolati karakterláncban megadott hitelesítő adatok érvénytelenek vagy lejárt" hibaüzenettel sikertelen.  

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása
Az index meghatározza a dokumentum mezőit, az attribútumokat és a keresési élményt formáló egyéb konstrukciókat.

Index létrehozása:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

Az indexek létrehozásáról az [Index létrehozása című](https://docs.microsoft.com/rest/api/searchservice/create-index)témakörben talál további információt.

### <a name="step-3-create-an-indexer"></a>3. lépés: Indexelő létrehozása
Az indexelő egy adatforrást egy célkeresési indexhez kapcsol össze, és ütemezést biztosít az adatfrissítés automatizálásához. 

Az index és az adatforrás létrehozása után készen áll az indexelő létrehozására:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Ez az indexelő kétóránként fut. (Az ütemezési időköz "PT2H" értékre van állítva.) Ha 30 percenként szeretne indexelőt futtatni, állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz öt perc. Az ütemezés nem kötelező; ha nincs megadva, az indexelő csak egyszer fut, amikor létrehozták. Az indexelőt azonban bármikor futtathatja igény szerint.   

Az Indexelő létrehozása API-ról további információt az [Indexelő létrehozása című](https://docs.microsoft.com/rest/api/searchservice/create-indexer)témakörben talál.

Az indexelő-ütemezések meghatározásáról az [Azure Cognitive Search indexelőinek ütemezése](search-howto-schedule-indexers.md)című témakörben olvashat bővebben.

## <a name="deal-with-different-field-names"></a>Különböző mezőnevek kezelése
Előfordulhat, hogy a meglévő indexben szereplő mezőnevek eltérnek a táblában szereplő tulajdonságnevektől. A mezőleképezések segítségével a tábla tulajdonságneveit a keresési indexben szereplő mezőnevekhez képezheti le. Ha többet szeretne megtudni a mezőleképezésekről, olvassa el [az Azure Cognitive Search indexelő mezőleképezéseinek bridge the differences between datasources and search indexesek](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Dokumentumkulcsok kezelése
Az Azure Cognitive Search, a dokumentum kulcs egyedileg azonosítja a dokumentumot. Minden keresési indexnek pontosan egy `Edm.String`kulcsfontosságú típusú mezővel kell rendelkeznie. A kulcsmező az indexhez hozzáadott bizonylatok hoz a szükséges. (Valójában ez az egyetlen szükséges mező.)

Mivel a táblasorok összetett kulccsal rendelkeznek, `Key` az Azure Cognitive Search létrehoz egy szintetikus mezőt, amely a partíciókulcs és a sorkulcs értékek összefűzése. Ha például egy sor PartitionKey `PK1` értéke és `RK1`a `Key` RowKey értéke, `PK1RK1`akkor a mező értéke .

> [!NOTE]
> Az `Key` érték érvénytelen karaktereket tartalmazhat a dokumentumbillentyűkben, például kötőjeleket. Az érvénytelen karaktereket a `base64Encode` [mezőleképezési funkcióval](search-indexer-field-mappings.md#base64EncodeFunction)oldhatja meg. Ha ezt teszi, ne feledje az URL szempontjából biztonságos Base64-kódolást használni a dokumentumkulcsok átadására a LookUp (Keresés) és hasonló API-hívások esetében is.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Növekményes indexelés és törlés észlelése
Ha úgy állít be egy táblaindexelőt, hogy ütemezés szerint fusson, az csak az `Timestamp` új vagy frissített sorokat indexeli újra, a sor értéke alapján. Nem kell megadnia a változásészlelési házirendet. A növekményes indexelés automatikusan engedélyezve van.

Annak jelzésére, hogy bizonyos dokumentumokat el kell távolítani az indexből, használhat egy helyreállítható törlési stratégiát. Sor törlése helyett adjon hozzá egy tulajdonságot, amely jelzi, hogy törölték, és állítson be egy lágy törlés-észlelési házirendet az adatforráson. A következő házirend például úgy véli, hogy egy `IsDeleted` sor törlődik, ha a sor nak van egy tulajdonsága, amelynek értéke: `"true"`

    PUT https://[service name].search.windows.net/datasources?api-version=2019-05-06
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

Alapértelmezés szerint az Azure Cognitive Search `Timestamp >= HighWaterMarkValue`a következő lekérdezési szűrőt használja: . Mivel az Azure-táblák nem rendelkeznek `Timestamp` másodlagos index a mezőben, az ilyen típusú lekérdezés teljes táblavizsgálatra van szükség, és ezért lassú a nagy táblák.


Az alábbiakban két lehetséges módszert olvashat a tábla indexelési teljesítményének javítására. Mindkét megközelítés a táblapartíciók használatára támaszkodik: 

- Ha az adatok természetesen több partíciótartományra is feloszthatók, hozzon létre egy adatforrást és egy megfelelő indexelőt minden partíciótartományhoz. Minden indexelőmost már fel kell dolgoznia csak egy adott partíciótartományt, ami jobb lekérdezési teljesítményt eredményez. Ha az indexelendő adatok kis számú rögzített partícióval rendelkeznek, még jobb: minden indexelő csak partíciós vizsgálat. Ha például egy partíciótartomány feldolgozásához adatforrást `000` szeretne `100`létrehozni, amelynek kulcsai a rendszerbe, használjon ehhez hasonló lekérdezést: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Ha az adatok at idő szerint particionálják (például minden nap vagy héten új partíciót hoz létre), vegye figyelembe a következő megközelítést: 
    - Az űrlap lekérdezésének `(PartitionKey ge <TimeStamp>) and (other filters)`használata: . 
    - Figyelze az indexelő állapotát a [Get Indexer Status API használatával,](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)és rendszeresen frissítse a `<TimeStamp>` lekérdezés állapotát a legutóbbi sikeres magas vízjel érték alapján. 
    - Ezzel a megközelítéssel, ha egy teljes újraindexelés, az indexelő alaphelyzetbe állítása mellett az adatforrás-lekérdezést kell alaphelyzetbe állítania. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Segítsen nekünk az Azure Cognitive Search jobbá tenni
Ha van funkció kérések vagy ötletek et fejlesztések, küldje el őket a mi [UserVoice oldalon](https://feedback.azure.com/forums/263029-azure-search/).
