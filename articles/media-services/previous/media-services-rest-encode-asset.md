---
title: Egy Azure-objektum kódolása a Media Encoder Standard használatával |} A Microsoft Docs
description: Ismerje meg az Azure Media Services médiatartalmak kódolása a Media Encoder Standard használatával. Kódminták REST API-val.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: 34652400acaf2efca3648bb4c7cde795861c3101
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50247929"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Adategység kódolása a Media Encoder Standard használatával hogyan
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portál](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Áttekintés
A digitális videót továbbíthasson az interneten keresztül, akkor kell tömörítését. Digitális videofájlok nagy, és előfordulhat, hogy az interneten, vagy az ügyfelek eszközök jelennek meg megfelelően túl nagy. Kódolás az a folyamat tömöríti az audio- és videofájlokat, így az ügyfelek tekintheti meg az adathordozó.

Kódolási feladatot hajt végre az Azure Media Services leggyakoribb feldolgozási műveletek tartoznak. A kódolási feladat a médiafájlokat alakítja át egy meghatározott kódolásból egy másikra. Kódolás, ha a Media Services beépített kódoló (Media Encoder Standard) is használhatja. Egy Media Services-partner által biztosított kódoló is használhatja. Külső kódolókkal az Azure Marketplace-en keresztül érhetők el. Megadhatja, hogy a részletek a kódolási feladatokat a kódolóra meghatározott előre beállított karakterláncok használatával, vagy előre beállított konfigurációs fájlok használatával. A rendelkezésre álló készletek típusú megtekintéséhez lásd: [feladat készletek Media Encoder standard](http://msdn.microsoft.com/library/mt269960).

Minden egyes feladat elvégezni kívánt feldolgozási típusától függően egy vagy több feladat is rendelkezhet. A REST API-n keresztül hozhat létre feladatokat és azok kapcsolódó tevékenységeket a két módszer egyikével:

* Feladatok lehetnek a tevékenységek navigációs tulajdonság entitások feladat keresztül definiálva van soron belül.
* OData kötegelt feldolgozás használja.

Javasoljuk, hogy a forrásfájlok mindig kódolandó egy adaptív sávszélességű MP4-készletet, és alakítsa át a készlet a kívánt formátumra használatával [dinamikus csomagolási](media-services-dynamic-packaging-overview.md).

Ha a kimeneti objektum tárolása titkosítva, konfigurálnia kell az adategység továbbítási házirendjét. További információkért lásd: [objektumtovábbítási szabályzat konfigurálása](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Megfontolandó szempontok

A Media Services entitások elérésekor a be kell állítani a HTTP-kérelmekre a meghatározott fejlécmezők és értékek. További információkért lásd: [beállítása a Media Services REST API-k fejlesztését](media-services-rest-how-to-use.md).

Mielőtt elkezdené a médiaelemzés médiafeldolgozói hivatkozik, ellenőrizze, hogy rendelkezik-e a megfelelő adathordozót megadva processzor azonosítóját. További információkért lásd: [médiafeldolgozók első](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-t kapcsolódás információkért lásd: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Hozzon létre egy feladatot az egy kódolási feladat
> [!NOTE]
> Dolgozik, a Media Services REST API-val, amikor a következő szempontokat kell figyelembe venni:
>
> A Media Services entitások elérésekor a be kell állítani a HTTP-kérelmekre a meghatározott fejlécmezők és értékek. További információkért lásd: [Media Services REST API fejlesztési telepítő](media-services-rest-how-to-use.md).
>
> Ha JSON használatával, és adja meg a használandó a **__metadata** kulcsszó a kérelemben (például, hogy hivatkozási csatolt objektum), be kell a **elfogadás** fejlécet [arészletesJSONformátumban](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Fogadja el: application/json; odata = részletes.
>
>

Az alábbi példa bemutatja, hogyan hozhat létre, és a egy feladatot tartalmazó adott feloldási és minőségi videó kódolásához beállítása egy feladat küldése. A Media Encoder standarddal kódol, használhatja a megadott feladat konfigurációs készletek [Itt](http://msdn.microsoft.com/library/mt269960).

Kérés:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Válasz:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Állítsa be a kimeneti objektum nevét
Az alábbi példa bemutatja, hogyan assetName attribútum:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Megfontolandó szempontok
* TaskBody tulajdonságai szövegkonstans XML segítségével meghatározhatja a beviteli számát, vagy a tevékenység által használt eszközök kimeneti. A feladat a cikk az XML-séma definíció az XML-fájl tartalmazza.
* A TaskBody definíciójában minden egyes belső értékét <inputAsset> és <outputAsset> JobInputAsset(value) vagy JobOutputAsset(value) kell beállítani.
* Több kimeneti objektumok feladatonként. Egy JobOutputAsset(x) csak egyszer használhatók fel a feladat a feladat kimeneteként.
* Megadhatja a feladat bemeneti adategység JobInputAsset vagy JobOutputAsset.
* Feladatok kell nem alkotnak kört.
* A paraméter, amely JobInputAsset vagy JobOutputAsset adja át az adott eszköz számára index értékét jelöli. A tényleges eszközöket a feladat definícióját a InputMediaAssets és OutputMediaAssets navigációs tulajdonságok vannak meghatározva.
* A Media Services OData v3 épül, mert az egyes eszközöket a InputMediaAssets és OutputMediaAssets navigációs tulajdonság gyűjteményeket a hivatkozott keresztül egy "__metadata: uri" név-érték pár.
* Egy vagy több eszközt, amely a Media Services szolgáltatásban létrehozott InputMediaAssets rendeli hozzá. A rendszer OutputMediaAssets hoznak létre. Ezek nem hivatkozhat egy meglévő eszköz.
* OutputMediaAssets elnevezheti az assetName attribútum használatával. Ha ez az attribútum nem szerepel, akkor a OutputMediaAsset neve, függetlenül a belső szöveges értéket a <outputAsset> elem van, a feladat név-érték, vagy a feladat azonosítóérték (abban az esetben, ha a Name tulajdonság nincs definiálva) utótaggal. Például, ha a "Mintaként" assetName értékét, majd a OutputMediaAsset neve tulajdonság értéke "Sample." Azonban ha assetName értékét nem állította be, de adta meg az "NewJob" a feladat nevét, majd a OutputMediaAsset neve lenne "_NewJob JobOutputAsset (érték)."

## <a name="create-a-job-with-chained-tasks"></a>Hozzon létre egy feladatot a kapcsolt műveletek
A sok alkalmazás-forgatókönyvek fejlesztőknek létrehozni kívánt feldolgozási feladatok sorozatát. A Media Services szolgáltatásban hozzon létre egy kapcsolt műveletek sorát. Minden tevékenység eltérő feldolgozási lépést hajt végre, és különböző médiafeldolgozók szolgál. A kapcsolt műveletek is kiosztják az eszköz egy feladatot a másikra, lineáris sorozatát feladatokat végez az eszköz. Egy feladat végrehajtott feladatok azonban nem szükségesek sorrendben kell. Amikor hoz létre egy kapcsolt feladat, a kapcsolt **ITask** objektumok jönnek létre egyetlen **IJob** objektum.

> [!NOTE]
> Jelenleg legfeljebb 30 feladatok száma alapján történik. Ha összekapcsolja a több mint 30 feladatok van szüksége, hozzon létre a feladatok tárolására egynél több feladatot.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
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

* Egy feladat legalább két feladatot kell rendelkeznie.
* Legalább egy tevékenységet, amelynek nincs egy másik feladat a feladat kimenetének kell lennie.

## <a name="use-odata-batch-processing"></a>Használja az OData kötegelt feldolgozás
Az alábbi példa bemutatja, hogyan OData kötegelt feldolgozási feladatok és tevékenységek létrehozásához használandó. Kötegelt feldolgozás kapcsolatos tudnivalókat lásd: [Open Data (OData) protokollnak kötegelt feldolgozási](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
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
    Authorization: Bearer <ENCODED JWT TOKEN> 
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
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Hozzon létre egy feladatot egy JobTemplate használatával
Adategységek feladatok készletének használatával dolgozza fel, amikor egy JobTemplate használja, és adja meg az alapértelmezett feladat készletek, vagy a sorrendet, feladatok.

Az alábbi példa bemutatja, hogyan hozzon létre egy JobTemplate egy TaskTemplate, amelyek definiálva van soron belül van. A TaskTemplate használja a Media Encoder Standard a MediaProcessor adategységfájlon kódolása. Azonban más MediaProcessors használható is.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Ellentétben más Media Services-entitásokkal egy új GUID azonosító minden TaskTemplate definiálása és tegyük a taskTemplateId és azonosító tulajdonságot a kérelem törzsében. A tartalom azonosítási séma a rendszer az Azure Media Services entitások azonosítása leírt kell követnie. Ezenkívül JobTemplates nem lehet frissíteni. Ehelyett hozzon létre egy újat a frissített módosításokat.
>
>

Ha ez sikeres, a következő választ adja vissza:

    HTTP/1.1 201 Created

    . . .


Az alábbi példa bemutatja, hogyan hozhat létre egy feladatot, amely JobTemplate azonosítóra hivatkozik:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Ha ez sikeres, a következő választ adja vissza:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Speciális kódolási funkciókat megismerése
* [Miniatűrök létrehozása](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Miniatűrök létrehozása a kódolás során](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Videók körülvágása a kódolás során](media-services-crop-video.md)
* [Kódolási beállításkészletek testreszabása](media-services-custom-mes-presets-with-dotnet.md)
* [Átfedő vagy a kép a videó küszöbérték](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
Most, hogy már tudja, hogyan hozzon létre egy feladatot az adategység kódolása, lásd: [a Media Services a feladat előrehaladásának ellenőrzése annak](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Lásd még
[A Médiaelemzés Médiafeldolgozói beolvasása](media-services-rest-get-media-processor.md)
