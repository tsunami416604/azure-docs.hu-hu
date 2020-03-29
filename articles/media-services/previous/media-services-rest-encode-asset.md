---
title: Azure-eszköz kódolása a Media Encoder Standard használatával | Microsoft dokumentumok
description: Megtudhatja, hogy miként kódolhat médiatartalmakat az Azure Media Servicesben a Media Encoder Standard használatával. A kódminták REST API-t használnak.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 6854400f2152a5952a7b24dbd860d7ad4bfc943d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774919"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Eszköz kódolása a Media Encoder Standard használatával
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [Többi](media-services-rest-encode-asset.md)
> * [Portál](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Áttekintés

Ahhoz, hogy digitális videót jelenítsen meg az interneten keresztül, tömörítenie kell az adathordozót. A digitális videofájlok nagyok, és túl nagyok az interneten keresztüli kézbesítéshez, illetve az ügyfelek eszközeinek megfelelő megjelenítéséhez. A kódolás a video- és hanganyagok tömörítésének folyamata, hogy ügyfelei megtekinthessék a médiatartalmakat.

A kódolási feladatok az Azure Media Services egyik leggyakoribb feldolgozási műveletei. Kódolási feladatokat hozhat létre a médiafájlok egyik kódolásból a másikba történő konvertálásához. Kódoláskor használhatja a Media Services beépített kódolóját (Media Encoder Standard). A Media Services-partnerek által biztosított kódolót is használhatja. A külső kódolók az Azure Marketplace-en keresztül érhetők el. A kódolási feladatok részleteit a kódolóhoz definiált előre beállított karakterláncok vagy előre beállított konfigurációs fájlok használatával adhatja meg. Az elérhető készletek típusairól a [Feladatkészletek a Media Encoder Standard alkalmazáshoz című témakörben tekinthető](https://msdn.microsoft.com/library/mt269960)meg.

Minden feladatnak lehet egy vagy több feladata a végrehajtani kívánt feldolgozás típusától függően. A REST API-n keresztül kétféleképpen hozhat létre feladatokat és a kapcsolódó feladatokat:

* A feladatok szövegközien definiálhatók a Feladatok navigációs tulajdonságon keresztül a Feladat entitásokon.
* OData kötegelt feldolgozás használata.

Javasoljuk, hogy a forrásfájlokat mindig adaptív sávszélességű MP4-készletbe kódolja, majd dinamikus [csomagolással](media-services-dynamic-packaging-overview.md)konvertálja a készletet a kívánt formátumra.

Ha a kimeneti eszköz titkosítva van, konfigurálnia kell az eszközkézbesítési szabályzatot. További információ: [Asset delivery policy configur.](media-services-rest-configure-asset-delivery-policy.md)

## <a name="considerations"></a>Megfontolandó szempontok

Amikor a Media Services entitásait éri el, meghatározott fejlécmezőket és értékeket kell beállítania a HTTP-kérelmekben. További információt a [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md)című témakörben talál.

Mielőtt elkezdené a médiaprocesszorokra való hivatkozást, ellenőrizze, hogy a megfelelő médiaprocesszor-azonosítóval rendelkezik-e. További információt a [Médiaprocesszorok beszereznie című témakörben talál.](media-services-rest-get-media-processor.md)

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz való csatlakozásról az [Access the Azure Media Services API azure AD-hitelesítéssel című témakörben](media-services-use-aad-auth-to-access-ams-api.md)talál további információt. 

## <a name="create-a-job-with-a-single-encoding-task"></a>Feladat létrehozása egyetlen kódolási feladattal

> [!NOTE]
> Amikor a Media Services REST API-val dolgozik, a következő szempontok érvényesek:
>
> Amikor a Media Services entitásait éri el, meghatározott fejlécmezőket és értékeket kell beállítania a HTTP-kérelmekben. További információt a [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md)című témakörben talál.
>
> A JSON használatakor és a **__metadata** kulcsszó használatának megadásakor (például csatolt objektumra való hivatkozáshoz) az **Elfogadás** fejlécet [JSON Verbose formátumra kell állítania:](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/)Elfogadás: alkalmazás/json;odata=verbose.
>
>

A következő példa bemutatja, hogyan hozhat létre és könyvelheti fel azt a feladatot, amelyen egy feladat van beállítva, hogy egy videót egy adott felbontásban és minőségben kódoljon. Ha media encoder Standard szolgáltatással kódol, használhatja [az itt](https://msdn.microsoft.com/library/mt269960)megadott feladatkonfigurációs készleteket.

Kérés:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Válasz:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>A kimeneti eszköz nevének beállítása
A következő példa bemutatja, hogyan állíthatja be az assetName attribútumot:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Megfontolandó szempontok
* A TaskBody tulajdonságoknak literális XML-t kell használniuk a feladat által használt bemeneti vagy kimeneti eszközök számának meghatározásához. A feladatcikk az XML XML-sémadefinícióját tartalmazza.
* A TaskBody definícióban minden `<inputAsset>` belső `<outputAsset>` érték, és meg kell adni a JobInputAsset(érték) vagy JobOutputAsset(value).
* Egy feladatnak több kimeneti eszköze is lehet. Egy JobOutputAsset(x) csak egyszer használható egy feladat kimeneteként egy feladatban.
* Megadhatja jobinputasset vagy JobOutputAsset egy feladat bemeneti eszközeként.
* A feladatok nem képeznek ciklust.
* A JobInputAsset vagy JobOutputAsset értékparaméter egy eszköz indexértékét jelöli. A tényleges eszközök vannak definiálva az InputMediaAssets és OutputMediaAssets navigációs tulajdonságok a feladat entitás definícióján.
* Mivel a Media Services az OData v3-ra épül, az InputMediaAssets és az OutputMediaAssets navigációs tulajdonsággyűjtemények egyes eszközeire egy "__metadata: uri" név-érték pár hivatkozik.
* Az InputMediaAssets leképezi a Media Services szolgáltatásban létrehozott egy vagy több eszközt. OutputMediaAssets jönlétre a rendszer. Nem hivatkoznak egy létező eszközre.
* A OutputMediaAssets az assetName attribútum használatával nevezhető el. Ha ez az attribútum nincs jelen, akkor az OutputMediaAsset neve az `<outputAsset>` elem belső szöveges értékétől független, vagy a Feladat név érték vagy a Feladatazonosító érték utótaggal (abban az esetben, ha a Name tulajdonság nincs definiálva). Ha például az eszköznév értékét "Minta" értékre állítja, akkor az OutputMediaAsset Name tulajdonság "Minta" értékű lesz. Ha azonban nem állított be értéket az assetName értékhez, de a feladat nevét "NewJob" értékre állította, akkor az OutputMediaAsset name "JobOutputAsset(value)_NewJob" lesz.

## <a name="create-a-job-with-chained-tasks"></a>Feladat létrehozása láncolt feladatokkal
Számos alkalmazási forgatókönyv esetén a fejlesztők feldolgozási feladatok sorozatát szeretnék létrehozni. A Media Services szolgáltatásban láncolt feladatok sorozatát hozhatja létre. Minden feladat különböző feldolgozási lépéseket hajt végre, és különböző médiaprocesszorokat használhat. A láncolt feladatok átadhatnak egy eszközt egyik tevékenységből a másikba, lineáris feladatsorozatot hajtva végre az eszközön. A feladatban végrehajtott feladatoknak azonban nem kell sorrendben lenniük. Láncolt feladat létrehozásakor a láncolt **ITask-objektumok** egyetlen **IJob** objektumban jönnek létre.

> [!NOTE]
> Jelenleg feladatonként legfeljebb 30 feladat lehet. Ha 30-nál több tevékenységet kell láncolnia, hozzon létre egynél több feladatot a tevékenységek hez.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
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
Feladatláncolás engedélyezése:

* Egy feladatnak legalább két feladatból kell rendelkeznie.
* Legalább egy olyan feladatnak kell lennie, amelynek bemenete egy másik feladat kimenete a feladatban.

## <a name="use-odata-batch-processing"></a>OData kötegelt feldolgozás használata
A következő példa bemutatja, hogyan lehet az OData kötegelt feldolgozás segítségével feladatot és feladatokat létrehozni. A kötegelt feldolgozással kapcsolatos információkért lásd: [Open Data Protocol (OData) Batch Processing](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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
    x-ms-version: 2.19
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
    x-ms-version: 2.19
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Feladat létrehozása Feladatsablon használatával
Ha több eszközt dolgoz fel közös feladatkészlettel, a JobTemplate segítségével adja meg az alapértelmezett feladatkészleteket, vagy állítsa be a feladatok sorrendjét.

A következő példa bemutatja, hogyan hozhat létre feladatsablont egy szövegközi feladatsablonnal. A TaskTemplate a Media Encoder Standard ot használja MediaProcessor néven az eszközfájl kódolásához. Azonban más MediaProcessors is használható.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Más Media Services-entitásokkal ellentétben meg kell határoznia egy új GUID-azonosítót minden TaskTemplate,és helyezze el a taskTemplateId és Id tulajdonság a kérelem törzsében. A tartalomazonosítási sémának követnie kell az Azure Media Services-entitások azonosításában leírt sémát. A JobTemplates nem frissíthető. Ehelyett létre kell hoznia egy újat a frissített módosításokkal.
>
>

Ha sikeres, a következő választ adja vissza:

    HTTP/1.1 201 Created

    . . .


A következő példa bemutatja, hogyan hozhat létre olyan feladatot, amely jobsablon-azonosítóra hivatkozik:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Ha sikeres, a következő választ adja vissza:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Speciális kódolási funkciók a felfedezéshez
* [Miniatűrök létrehozása](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Bélyegképek létrehozása kódolás közben](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Videók körülvágása kódolás közben](media-services-crop-video.md)
* [Kódolási készletek testreszabása](media-services-custom-mes-presets-with-dotnet.md)
* [Videó fedvényezése vagy vízjellel képpel](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
Most, hogy már tudja, hogyan hozhat létre egy feladat kódolására egy eszköz, olvassa el [a Hogyan ellenőrizheti a feladat előrehaladását a Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Lásd még
[Médiaprocesszorok bekése](media-services-rest-get-media-processor.md)
