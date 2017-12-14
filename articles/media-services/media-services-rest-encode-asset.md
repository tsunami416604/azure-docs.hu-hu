---
title: "Egy Azure-objektum kódolása a Media Encoder Standard használatával hogyan |} Microsoft Docs"
description: "Útmutató: Media Encoder Standard segítségével az Azure Media Services media tartalom kódolása. Kódminták REST API-t használja."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: a58cf1402d31538cb4d9753a66846f683839810c
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Egy eszköz kódolással Media Encoder Standard használatával
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portál](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Áttekintés
Digitális videót továbbíthasson az interneten, meg kell végezni a tartalom tömörítését. Digitális videofájlok nagyok, és lehet, hogy az interneten keresztül, vagy a felhasználók eszközökhöz megfelelően megjeleníthető túl nagy. Kódolás tömörítés videó és hang, így az ügyfelek megtekintheti a media során a rendszer.

Kódolási feladatok olyan Azure Media Services a leggyakrabban használt feldolgozási műveletek. A kódolási feladat a médiafájlokat alakítja át egy meghatározott kódolásból egy másikra. Amikor kódolásához, a Media Services beépített kódoló (Media Encoder Standard) is használhatja. Egy Media Services partner által biztosított kódoló is használható. Külső kódolókkal az Azure piactéren keresztül érhetők el. Megadhatja a kódolási feladatokhoz a kódoló-készlet karakterláncokat használatával, vagy előre definiált konfigurációs fájlok részletes adatait. A készletek rendelkezésre álló típusú, olvassa el [feladat készletek a Media Encoder Standard](http://msdn.microsoft.com/library/mt269960).

Minden feladat elvégzéséhez kívánt feldolgozási típusától függően egy vagy több feladat is rendelkezhetnek. A REST API használatával hozhat létre feladatokat és azok kapcsolódó feladatok az alábbi két módszer egyikével:

* Feladatok lehet meghatározott beágyazott a feladatok navigációs tulajdonság a feladat entitások keresztül.
* OData kötegfeldolgozási használja.

Javasoljuk, hogy mindig kódolása egy adaptív sávszélességű MP4 állítsa be a forrásfájlokat, és majd a készlet át kell alakítania a kívánt formátum használatával [dinamikus becsomagolás](media-services-dynamic-packaging-overview.md).

Ha az kimeneti adategységen tárolótitkosítást alkalmaz, konfigurálnia kell az adategység továbbítási házirendjét. További információkért lásd: [objektumtovábbítási szabályzat konfigurálása](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Megfontolandó szempontok

A Media Services entitások elérésekor be kell meghatározott fejlécmezők és értékek a HTTP-kérelmekre. További információkért lásd: [a Media Services REST API fejlesztési telepítő](media-services-rest-how-to-use.md).

Mielőtt media processzorok hivatkozik, győződjön meg arról, hogy rendelkezik-e a megfelelő adathordozót processzor azonosítóját. További információkért lásd: [media processzorok beolvasása](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz kapcsolódáshoz információkért lásd: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Hozzon létre egy feladatot egyetlen kódolási feladat
> [!NOTE]
> Dolgozunk a Media Services REST API-t, ha a következők érvényesek:
>
> A Media Services entitások elérésekor be kell meghatározott fejlécmezők és értékek a HTTP-kérelmekre. További információkért lásd: [Media Services REST API fejlesztési telepítő](media-services-rest-how-to-use.md).
>
> Ha JSON használatával és használandó megadásával a **__metadata** kulcsszó a kérés (például, hogy a hivatkozás csatolt objektum), meg kell adni a **elfogadás** fejlécének [részletes JSON formátumban](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Fogadja el: az application/json; odata = részletes.
>
>

A következő példa bemutatja, hogyan hozhat létre, és indítsa el egy feladat az adott feloldási és minőségi videó kódolására beállítani egy feladat. A Media Encoder Standard kódol, használhatja a megadott feladat konfigurációs készletek [Itt](http://msdn.microsoft.com/library/mt269960).

A kérelem:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Válasz:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>A kimeneti adategységen nevének megadása
A következő példa bemutatja, hogyan assetName attribútum:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Megfontolandó szempontok
* TaskBody tulajdonságok literális XML segítségével határozza meg a bemeneti számát, vagy a tevékenység által használt eszközök kimeneti. A feladat a cikk az XML-Schema definíció az XML-fájl tartalmazza.
* A TaskBody definícióban minden belső értékének <inputAsset> és <outputAsset> JobInputAsset(value) vagy JobOutputAsset(value) be kell állítani.
* Kimeneti adategységek feladatonként. Egy JobOutputAsset(x) csak egyszer használható egy feladatot a feladatok kimeneteként.
* A tevékenység bemeneti eszközként JobInputAsset vagy JobOutputAsset adhat meg.
* Feladatok kell nem alkotnak hurkot.
* Az érték paraméter jelzi, hogy átadta JobInputAsset vagy JobOutputAsset az eszközhöz tartozó index értékét jelöli. A tényleges eszközök entitás feladatdefinícióban InputMediaAssets és OutputMediaAssets navigációs tulajdonságok vannak definiálva.
* A Media Services OData v3 épül, mert az egyes eszközök a InputMediaAssets és OutputMediaAssets navigációs tulajdonság gyűjteményeket hivatkozott keresztül a "__metadata: uri" név-érték pár.
* Egy vagy több olyan eszközt, a Media Services InputMediaAssets rendeli hozzá. A rendszer OutputMediaAssets hozza létre. Egy meglévő eszköz nem hivatkozó.
* OutputMediaAssets elnevezheti a assetName attribútum használatával. Ha ez az attribútum nincs jelen, akkor a OutputMediaAsset nevét bármilyen belső szöveges értékét a <outputAsset> eleme az előtagja pedig a feladat nevének értékét, vagy a feladat-azonosító értéke (abban az esetben, ha a Name tulajdonság nincs megadva). Például ha assetName "Mintájának" értéket állítsa be, majd a OutputMediaAsset neve tulajdonság értéke "Minta." Azonban ha assetName értékének beállítása nem, de adta meg az "NewJob" a feladat nevét, majd a OutputMediaAsset név lesz "_NewJob JobOutputAsset (érték)."

## <a name="create-a-job-with-chained-tasks"></a>Hozzon létre egy feladatot láncolt feladatok
Számos alkalmazás forgatókönyvben az fejlesztők kíván létrehozni a feldolgozási feladatok sorozata. A Media Services szolgáltatásban láncolt feladatok sorozatát hozhat létre. Minden tevékenység különböző feldolgozó lépéseket hajtja végre, és különböző media processzorok használhatja. A láncolt feladatokat is kiosztják egy eszköz egy feladat a másikra, az eszköz lineáris sorozatát feladatok végrehajtására. A feladatok a feladatokat azonban nem szükségesek sorrendben kell. Amikor a láncolt láncolt feladat létrehozásához **ITask** objektumok létrehozásakor egyetlen **IJob** objektum.

> [!NOTE]
> Jelenleg legfeljebb 30 tevékenységek maximális száma feladat. Ha több mint 30 feladatok láncolt van szüksége, hozzon létre több feladatot a feladatok tárolására.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


### <a name="considerations"></a>Megfontolandó szempontok
Ahhoz, hogy a feladat-láncolás:

* Egy feladat legalább két tevékenységet kell rendelkeznie.
* Legalább egy tevékenységet, amelynek a bemeneti érték a feladat egy másik feladat kimenetének kell lennie.

## <a name="use-odata-batch-processing"></a>Használhatja a kötegelt feldolgozást OData
A következő példa bemutatja, hogyan OData kötegfeldolgozási használata a feladatok és a feladatok létrehozásához. Kötegfeldolgozási információkért lásd: [nyitott Data (OData) protokollnak kötegelt feldolgozása](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net


    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Hozzon létre egy feladatot a JobTemplate használatával
Több eszköz feladatok közös beállításkészletre segítségével dolgozza fel, ha a egy JobTemplate adhatja meg az alapértelmezett feladat készletek vagy feladatok sorrendjének beállításához.

A következő példa bemutatja, hogyan hozzon létre egy JobTemplate egy TaskTemplate, amely meghatározott beágyazott. A TaskTemplate használja a Media Encoder Standard a MediaProcessor kódolására az adategységfájlon. Azonban más MediaProcessors használható is.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <token value>
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Eltérően egyéb Media Services entitások egy új GUID azonosító megadása minden TaskTemplate és naplózza azt a taskTemplateId és az Id tulajdonság a kérés törzsében. A tartalom azonosítási séma hajtsa végre a rendszer az Azure Media Services entitások azonosítása leírt. JobTemplates is, nem lehet frissíteni. Ehelyett hozzon létre egy újat a frissített módosításokat.
>
>

Ha sikeres, a következő választ ad vissza:

    HTTP/1.1 201 Created

    . . .


A következő példa bemutatja, hogyan hozzon létre egy feladatot, amely JobTemplate azonosítóra hivatkozik:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <token value>
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Ha sikeres, a következő választ ad vissza:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Speciális kódolás szolgáltatások felfedezése
* [A miniatűrök létrehozásának módját](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Miniatűrök létrehozása során kódolás](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Kódolás során körülvágása videók](media-services-crop-video.md)
* [Kódolási készletek testreszabása](media-services-custom-mes-presets-with-dotnet.md)
* [Átfedő, vagy olyan képpel videó vízjel](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
Most, hogy tudja, hogyan hozzon létre egy feladatot, amely egy eszköz kódolására, lásd: [ellenőrzése a feladat előrehaladását a Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Lásd még:
[Media processzorok beolvasása](media-services-rest-get-media-processor.md)
