---
title: Ismerkedés a többi segítségével igény szerinti tartalomtovábbítás |} Microsoft Docs
description: Ez az oktatóanyag végigvezeti a végrehajtási egy on igény szerinti tartalomtovábbító alkalmazást az Azure Media Services REST API használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: juliako
ms.openlocfilehash: f0241278343ba4383caef5bb52bc4f1ece2bec7e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Ismerkedés a többi segítségével igény szerinti tartalomtovábbítás
[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

A gyors üzembe helyezés végigvezeti a végrehajtási egy Video-on-Demand (VoD) tartalomtovábbító alkalmazást Azure Media Services (AMS) REST API-k használatával.

Az útmutató bemutatja a Media Services alapvető munkafolyamatait és a Media Services-fejlesztéshez szükséges leggyakoribb programozási objektumokat és feladatokat. Az oktatóanyag befejezése után tudunk vagy fokozatosan letölteni egy feltöltött, kódolt és letöltött példa médiafájlt.

A következő kép a Media Services OData-modellen alapuló VoD-alkalmazásfejlesztések során leggyakrabban használt objektumok közül mutat be néhányat.

Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételei elkezdje a Media Services REST API-khoz.

* Egy Azure-fiók. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Egy Media Services-fiók. A Media Services-fiók létrehozásáról a [Media Services-fiók létrehozása](media-services-portal-create-account.md) című cikk nyújt tájékoztatást.
* Media Services REST API-t fejlesztésével megértése. További információkért lásd: [Media Services REST API – áttekintés](media-services-rest-how-to-use.md).
* Az Ön által választott küldő HTTP-kérések és válaszok alkalmazás. Ez az oktatóanyag használja [Fiddler](http://www.telerik.com/download/fiddler).

A következő feladatokat a gyors üzembe helyezés láthatók.

1. A streamvégpont elindítása (az Azure Portal használatával).
2. Csatlakozás a Media Services-fiók REST API-t.
3. Hozzon létre egy új eszközt, és a REST API-t egy videofájl feltöltése.
4. Forrásfájl kódolása adaptív sávszélességű MP4-fájlsorozattá REST API-val be.
5. Tegye közzé az adategységet, és a streamelési és a progresszív letöltési URL-címet a REST API-t.
6. Tartalom lejátszása

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Az azonos házirend-azonosító akkor használja, ha mindig használja az ugyanazon nap / hozzáférési engedélyek, például a lokátorokat, amelyek célja, hogy továbbra is érvényben hosszú ideje (nem feltöltés házirendek) házirendek. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

Ebben a cikkben használt AMS REST entitások kapcsolatos részletekért lásd: [Azure Media Services REST API-referencia](/rest/api/media/services/azure-media-services-rest-api-reference). Lásd még [Azure Media Services alapfogalmaiért](media-services-concepts.md).

>[!NOTE]
>A Media Services entitások elérésekor be kell meghatározott fejlécmezők és értékek a HTTP-kérelmekre. További információkért lásd: [a Media Services REST API fejlesztési telepítő](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>A streamvégpont elindítása az Azure Portal használatával

Az Azure Media Services használatakor egyik leggyakoribb forgatókönyve elkötelezett videó adaptív sávszélességű streamelés keresztül. A Media Services dinamikus csomagolást biztosít, amelynek köszönhetően adaptív sávszélességű, MP4 formátumban kódolt tartalmait a Media Services által támogatott streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming) továbbíthatja igény szerint, mindezt anélkül, hogy az adott formátumban előcsomagolt verziót tárolna.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie.

A streamvégpont elindításához tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a Settings (Beállítások) ablak Streaming endpoints (Streamvégpontok) elemére.
3. Kattintson az alapértelmezett streamvégpontra.

    Megjelenik a DEFAULT STREAMING ENDPOINT DETAILS (Alapértelmezett streamvégpont adatai) ablak.

4. Kattintson a Start ikonra.
5. Mentse a módosításokat a Save (Mentés) gombra kattintva.

## <a id="connect"></a>Csatlakozás a Media Services-fiók REST API-hoz

Az AMS API-hoz kapcsolódáshoz információkért lásd: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

## <a id="upload"></a>Hozzon létre egy új eszközt, és a REST API-t egy videofájl feltöltése

A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. A **eszköz** entitás tartalmazhat videót, hang, képek, miniatűröket, szöveg nyomon követi és feliratfájlokat fájlokat (és a mindezen fájlok metaadatait.)  Ha a fájlok feltöltése az objektumba, a lesz biztonságosan tárolva a tartalom további feldolgozás és adatfolyam-felhő.

Az értékeket, amelyeket meg kell adnia egy eszköz létrehozásakor egyik adategység-létrehozási lehetőségek. A **beállítások** tulajdonsága egy számbavételi érték, amely leírja a titkosítási beállításokat, hogy egy eszköz hozhatók létre. Érvényes értéket kell az alábbi listán, nem a lista értékeinek kombinációja értékek egyikét:

* **Nincs** = **0** – nincs titkosítás. Ez a beállítás használatakor a tartalom nem védett átvitel, sem tárolás közben.
    Ha egy MP4-fájlt progresszív letöltés útján tervez továbbítani, használja ezt a lehetőséget.
* **StorageEncrypted** = **1** - titkosítja a tiszta tartalom helyileg az AES-256 bites titkosítás használata, és feltölti azt Azure Storage helyén titkosítása. A Storage-titkosítással védett adategységek titkosítása a kódolás előtt automatikusan fel lesz oldva, és egy titkosított fájlrendszerbe kerülnek; az új kimeneti adategységként való újbóli feltöltés előtt pedig lehetőség van az újbóli titkosításukra. A Storage-titkosítás elsősorban akkor hasznos, ha a kiváló minőségű bemeneti médiafájljait erős titkosítással szeretné védeni a lemezen való tároláskor.
* **CommonEncryptionProtected** = **2** -használja ezt a beállítást, ha már titkosítva és védve általános titkosítás vagy a PlayReady DRM által (például Smooth Streaming tartalom védett PlayReady DRM).
* **EnvelopeEncryptionProtected** = **4** – használja ezt a beállítást, ha AES által titkosított HLS. A fájlok kell kódolni és Transform Manager használatával titkosított.

### <a name="create-an-asset"></a>Egy eszköz létrehozása
Az eszköz egy olyan tároló, típusok vagy a Media Services, beleértve a videó, hang, képeket, miniatűröket, szöveges nyomon követi és feliratfájlokat fájlok objektumokat. A REST API-ban az eszköz létrehozásához POST kérést küld a Media Services és az eszköz minden tulajdonságadatokat helyezi el a kérés törzsében.

A következő példa bemutatja, hogyan hozzon létre egy eszközt.

**HTTP-kérelem**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**HTTP-válasz**

Ha sikeres, a következő adja vissza:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>Hozzon létre egy AssetFile
A [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entitás egy blob-tárolóban tárolt video- vagy fájlt jelöli. Egy eszköz fájl mindig társítva van egy eszköz, és egy eszköz tartalmazhat egy vagy több AssetFiles. A Media Services kódoló feladat sikertelen lesz, ha egy eszköz fájl objektumhoz nincs társítva egy digitális fájlhoz egy blob-tárolóban.

Miután a digitális adathordozójának fájl feltöltése a blob-tárolóba, az a **EGYESÍTÉSE** frissíti a AssetFile a médiafájl információit tartalmazó (lásd a témakör későbbi részében) HTTP-kérelem.

**HTTP-kérelem**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-válasz**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>A AccessPolicy írási engedéllyel rendelkező létrehozása
Fájlok feltöltése a blob-tárolóba, mielőtt írásra, hogy egy eszköz házirend jogosultságok a hozzáférés beállítása. Ehhez a AccessPolicies entitáskészlet HTTP-kérelmek POST. Adjon meg egy DurationInMinutes számot a létrehozása után, vagy egy belső kiszolgálót 500 hibaüzenetet kapja vissza válaszként. A AccessPolicies további információkért lásd: [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

A következő példa bemutatja, hogyan hozzon létre egy AccessPolicy:

**HTTP-kérelem**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**HTTP-válasz**

Ha sikeres, a következő választ ad vissza:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>A feltöltés URL-cím beszerzése

A tényleges feltöltés URL-címet kap, hozzon létre egy SAS-kereső. Keresők meghatározása a kezdési idő és a csatlakozási végpont típusú ügyfelek számára, szeretné, hogy egy eszköz lévő fájlok eléréséhez. Létrehozhat több lokátor entitás egy adott AccessPolicy és eszköz párhoz különböző ügyfélkérelmek és kell kezelni. A Lokátorokat mindegyikének használja a StartTime érték és a AccessPolicy DurationInMinutes értékének mennyi ideig egy URL-cím használható. További információkért lásd: [lokátor](https://docs.microsoft.com/rest/api/media/operations/locator).

A SAS URL-cím formátuma a következő:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Vegye figyelembe a következőket:

* Egy adott eszközhöz társított egyszerre legfeljebb öt egyedi keresők tartalmazhat. További információkért tekintse meg a lokátor.
* Ha szeretné azonnal töltse fel a fájlokat, akkor a StartTime érték az aktuális időpont előtt öt percet kell beállítania. Ennek az az oka lehet óra eltérésére az ügyfélszámítógép és a Media Services között. Ezenkívül a StartTime érték a következő dátum és idő formátumban kell lennie: éééé-hh-SSz (például "2014-05-23T17:53:50Z").    
* Előfordulhat, hogy a 30-40 második késleltetése, ha használható a lokátor létrehozása után. A probléma a SAS URL-cím és a forrás keresők egyaránt vonatkoznak.

További információ a SAS keresők: [ez](http://southworks.com/blog/2015/05/27/reusing-azure-media-services-locators-to-avoid-facing-the-5-shared-access-policy-limitation/) blog.

A következő példa bemutatja, hogyan egy SAS URL-cím lokátor létrehozása a kérés törzsében ("1" egy SAS-kereső) és egy az Igényalapú származási kereső "2" típusú tulajdonság által meghatározott módon. A **elérési** visszaadott tulajdonsága tartalmazza az URL-címet, fel kell töltenie a fájlt kell használnia.

**HTTP-kérelem**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**HTTP-válasz**

Ha sikeres, a következő választ ad vissza:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Egy blob storage tárolóba-fájl feltöltése
Miután a AccessPolicy és lokátor beállítása, a tényleges fájlt feltölteni egy Azure blob storage tárolót használja az Azure Storage REST API-k van. A fájlok blokkblobként kell feltöltenie. Lapblobokat nem Azure Media Services által támogatott.  

> [!NOTE]
> Hozzá kell adni a fájlnevet a lokátor feltölteni kívánt fájl **elérési** érték érkezett az előző szakaszban. Például: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .
>
>

Az Azure storage blobs munkavégzés további információkért lásd: [Blob szolgáltatás REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Frissítés a AssetFile
Most, hogy a fájl feltöltése a FileAsset méret (és más) adatainak frissítése. Példa:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-válasz**

Ha sikeres, a következő adja vissza:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>A lokátor és AccessPolicy törlése
**HTTP-kérelem**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-válasz**

Ha sikeres, a következő adja vissza:

    HTTP/1.1 204 No Content
    ...

**HTTP-kérelem**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP-válasz**

Ha sikeres, a következő adja vissza:

    HTTP/1.1 204 No Content
    ...

## <a id="encode"></a>Forrásfájl kódolása adaptív sávszélességű MP4-fájlsorozattá készletére

Választásával dolgozhat fel, a Media Services szolgáltatásban adathordozó eszközök kódolható, transmuxed teljesítményjellemzőit, és így tovább, miután előtt biztosítását az ügyfelek számára. Ezen tevékenységek több háttérbeli szerepkörpéldányhoz képest vannak ütemezve és futtatva a magas teljesítmény és rendelkezésre állás biztosítása érdekében. Ezeket a tevékenységeket feladatoknak nevezzük, és minden feladat áll el a valódi munkát az adategységfájlon (további információkért lásd: [feladat](/rest/api/media/services/job), [feladat](/rest/api/media/services/task) leírása).

Mivel korábban már említettük, amikor olyan Azure Media Services egyik leggyakoribb forgatókönyve az adaptív sávszélességű streamelés továbbítása az ügyfelek számára. A Media Services tudja dinamikusan csomagolni a adaptív sávszélességű MP4-fájlokat a következő formátumok egyikének: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

A következő szakasz bemutatja, hogyan hozzon létre egy feladatot, amely tartalmazza a kódolási feladat. A feladat Megadja, hogy nem alakítható át a mezzazine-fájlt használatával adaptív sávszélességű MP4 készletére **Media Encoder Standard**. A szakasz azt is bemutatja, hogyan figyelheti a feladat feldolgozása folyamatban van. Ha a folyamat befejeződik, tudná, létre a lokátorokat, amelyek szükségesek ahhoz, hogy regisztrálásával hozzáférhet az eszközeihez.

### <a name="get-a-media-processor"></a>Egy media processzor beolvasása
A Media Services szolgáltatásban a media processzor összetevő, amely egy meghatározott feldolgozási feladatot, például a kódolása, titkosítása vagy visszafejtése közben médiatartalmak konverzióval, kezeli. A kódolási feladat ebben az oktatóanyagban szereplő fogjuk használni a Media Encoder Standard.

A következő kódot a kódoló azonosítót kér.

**HTTP-kérelem**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-válasz**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Feladat létrehozása
Minden feladat elvégzéséhez kívánt feldolgozási típusától függően egy vagy több feladat is rendelkezhetnek. A REST API használatával hozhat létre feladatokat és azok kapcsolódó feladatok az alábbi két módszer egyikével: feladatok lehet meghatározott beágyazott feladat entitások navigációs tulajdonság a feladatok vagy OData kötegfeldolgozási révén. A Media Services SDK kötegfeldolgozási használja. Az ebben a cikkben található példakódok olvashatóság, azonban a feladatok nem definiált beágyazott. Kötegfeldolgozási információkért lásd: [nyitott Data (OData) protokollnak kötegelt feldolgozása](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

A következő példa bemutatja, hogyan hozhat létre, és indítsa el a feladat egy feladat beállítása adott feloldási és minőségi videó kódolására. A következő dokumentáció tartalmazza az összes a [készletek feladat](http://msdn.microsoft.com/library/mt269960) a Media Encoder Standard processzor által támogatott.  

**HTTP-kérelem**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**HTTP-válasz**

Ha sikeres, a következő választ ad vissza:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


Néhány fontos dolgot feladat kérésének figyelembe venni:

* TaskBody tulajdonságok literális XML segítségével határozza meg a bemeneti számát, vagy a tevékenység által használt eszközök kimeneti. A feladat a cikk az XML-Schema definíció az XML-fájl tartalmazza.
* A TaskBody definícióban minden belső értékének <inputAsset> és <outputAsset> JobInputAsset(value) vagy JobOutputAsset(value) be kell állítani.
* Kimeneti adategységek feladatonként. Egy JobOutputAsset(x) csak egyszer használható egy feladatot a feladatok kimeneteként.
* A tevékenység bemeneti eszközként JobInputAsset vagy JobOutputAsset adhat meg.
* Feladatok kell nem alkotnak hurkot.
* Az érték paraméter jelzi, hogy átadta JobInputAsset vagy JobOutputAsset index értékét jelöli az adott eszköz számára. A tényleges eszközök entitás feladatdefinícióban InputMediaAssets és OutputMediaAssets navigációs tulajdonságok vannak definiálva.

> [!NOTE]
> A Media Services OData v3 épül, mert az egyes eszközöket InputMediaAssets és OutputMediaAssets navigációs tulajdonság gyűjtemények a hivatkozott keresztül a "__metadata: uri" név-érték pár.
>
>

* A Media Services létrehozott egy vagy több eszköz InputMediaAssets rendeli hozzá. A rendszer OutputMediaAssets hozza létre. Egy meglévő eszköz azok többé ne hivatkozzanak.
* OutputMediaAssets elnevezheti a assetName attribútum használatával. Ha ez az attribútum nincs jelen, akkor a OutputMediaAsset nevét bármilyen belső szöveges értékét a <outputAsset> eleme az előtagja pedig a feladat nevének értékét, vagy a feladat-azonosító értéke (abban az esetben, ha a Name tulajdonság nincs megadva). Például ha assetName "Mintájának" értéket állítsa be, majd a OutputMediaAsset neve tulajdonság volna állítható be "Sample". Azonban ha nem adta meg az assetName értékét, de adta meg az "NewJob" a feladat nevét, majd a OutputMediaAsset neve lesz "JobOutputAsset (érték) _NewJob".

    A következő példa bemutatja, hogyan assetName attribútum:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Ahhoz, hogy a feladat-láncolás:

  * Egy feladat legalább két tevékenységet kell rendelkeznie.
  * Legalább egy tevékenységet, amelynek a bemeneti érték a feladat egy másik feladat kimenetének kell lennie.

További információ: [egy kódolási feladat létrehozása a Media Services REST API-val](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>A figyelő feldolgozása folyamatban van
Az állapot tulajdonság használatával lekérhető a feladat állapotát, az alábbi példában látható módon:

**HTTP-kérelem**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**HTTP-válasz**

Ha sikeres, a következő választ ad vissza:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Feladatok megszakítása
A Media Services lehetővé teszi a CancelJob funkción keresztül futó feladatot megszakítja. Ez a hívás adja vissza egy 400 hibakód megszakítása, ha állapotában megszűnik, ha, visszavonás, hiba, vagy befejeződött.

A következő példa bemutatja, hogyan hívhatja meg CancelJob.

**HTTP-kérelem**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Ha sikeres, nem az üzenet törzse 204 válaszkód is visszaad.

> [!NOTE]
> Kell URL-kódolja a feladatazonosítót (általában nb:jid:UUID: somevalue) Ha paraméterként átadja azt a CancelJob.
>
>

### <a name="get-the-output-asset"></a>A kimeneti eszköz beolvasása
A következő kód bemutatja, hogyan kérjen a kimeneti adategységen azonosítóját.

**HTTP-kérelem**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-válasz**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a id="publish_get_urls"></a>Tegye közzé az adategységet, és a streamelési és a progresszív letöltési URL-címet a REST API-n

Egy adategység továbbításához vagy letöltéséhez először a „közzététele” szükséges, egy kereső létrehozásával. A keresők biztosítják az adategységben található fájlokhoz való hozzáférést. A Media Services kétféle keresőtípust támogat: az OnDemandOrigin keresők médiatartalmak továbbításához használatosak (például MPEG DASH, HLS vagy Smooth Streaming), a hozzáférési jogosultságkód (SAS)-keresők pedig médiafájlok letöltéséhez. További információ a SAS keresők: [ez](http://southworks.com/blog/2015/05/27/reusing-azure-media-services-locators-to-avoid-facing-the-5-shared-access-policy-limitation/) blog.

A keresők létrehozása után az URL-továbbításához vagy letöltéséhez a fájlok hozhat létre.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie.

Egy Smooth Streaming URL-címnek a következő formátumban kell lennie:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Egy HLS-továbbítási URL-címnek a következő formátumban kell lennie:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Egy MPEG DASH-továbbítási URL-címnek a következő formátumban kell lennie:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Egy fájlok letöltéséhez használt SAS URL-címnek a következő formátumban kell lennie:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Ez a szakasz bemutatja az eszközök "közzététele" szükséges a következő feladatok elvégzéséhez.  

* A AccessPolicy olvasási engedéllyel rendelkező létrehozása
* Letölti a tartalmat egy SAS URL-cím létrehozása
* Hozzon létre egy forrás URL-CÍMÉT egy adatfolyam-tartalom

### <a name="creating-the-accesspolicy-with-read-permission"></a>A AccessPolicy olvasási engedéllyel rendelkező létrehozása
Letöltése, illetve bármely médiatartalom streaming előtt először egy AccessPolicy olvasási engedély megadása, és hozzon létre a megfelelő lokátor entitás, amely engedélyezi az ügyfelek mechanizmus típusát határozza meg. A rendelkezésre álló tulajdonságok további információkért lásd: [AccessPolicy Entitástulajdonságok](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

A következő példa bemutatja, hogyan adhatja meg a AccessPolicy olvasási engedéllyel az adott eszköz számára.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Ha sikeres, a 201 sikeres vissza ismertető a létrehozott AccessPolicy entitás. Ezután használhatja a AccessPolicy azonosítói mellett az eszköz a lokátor entitás létrehozása (például egy kimeneti eszköz) kézbesíteni szeretné a fájlt tartalmazó objektum azonosítója.

> [!NOTE]
> Ez a munkafolyamat alapvető megegyezik a fájlt feltölteni, amikor választásával dolgozhat fel egy eszköz (a korábban a jelen témakörben bemutatott volt). Is például fájlok feltöltését Ha Ön (vagy az ügyfelek) kell a fájlok azonnal, állítsa be a StartTime érték az aktuális időpont előtt öt perc. Ez a művelet szükség, mert előfordulhatnak óra eltérésére az ügyfél és a Media Services között. A StartTime érték a következő dátum és idő formátumúnak kell lennie: éééé-hh-SSz (például "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Letölti a tartalmat egy SAS URL-cím létrehozása
A következő kód bemutatja, hogyan kérhet egy médiafájlt létrehozott és a korábban feltöltött letöltésére használható URL-címet. A AccessPolicy engedélycsoport rendelkezik-e olvasási és a lokátor elérési út egy SAS letöltési URL-CÍMRE hivatkozik.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Ha sikeres, a következő választ ad vissza:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

A visszaadott **elérési** tulajdonsága tartalmazza az SAS URL-címet.

> [!NOTE]
> Tárolási titkosított tartalom letöltéséhez esetén kell manuálisan előtt azt visszafejteni, vagy a a tárolási visszafejtési MediaProcessor feldolgozási feladatban kimeneti feldolgozott fájlok számára egy OutputAsset szövegként, és majd letölteni, hogy az eszköz. A feldolgozási további információkért lásd: egy kódolási feladat létrehozása a Media Services REST API-val. SAS URL-cím keresők nem lehet frissíteni, azok létrehozását követően. Például egy frissített StartTime értékkel azonos lokátor nem használhat újra. Ez az SAS URL-címek jönnek létre módjával. Ha azt szeretné, egy kereső lejárta után egy letölthető eszköz eléréséhez, akkor egy új StartTime egy újat kell létrehoznia.
>
>

### <a name="download-files"></a>Fájlok letöltése
Miután a AccessPolicy és lokátor beállítása, letöltheti a fájlokat az Azure Storage REST API-k segítségével.  

> [!NOTE]
> Hozzá kell adni a fájlnevet a fájl a lokátor a letölteni kívánt **elérési** érték érkezett az előző szakaszban. Például: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .
>
>

Az Azure storage blobs munkavégzés további információkért lásd: [Blob szolgáltatás REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Hatására a kódolási feladat korábbi (kódolása az adaptív MP4-készlet) végrehajtott hogy több MP4-fájlokat fokozatosan lehet letölteni. Példa:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Hozzon létre egy adatfolyam-továbbítási URL-címet egy adatfolyam-tartalom
Az alábbi kód bemutatja, hogyan hozhat létre egy adatfolyam-továbbítási URL-cím keresőt:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Ha sikeres, a következő választ ad vissza:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Egy Smooth Streaming forrás URL-címet egy adatfolyam-továbbítási media Player adatfolyam, hozzá kell fűzni a tulajdonság a Smooth Streaming nevű fájl jegyzékfájlja "/ manifest" elérési.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Adatfolyam HLS, hozzáfűző (formátum = m3u8-aapl) után a "/ manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Adatfolyam MPEG DASH, hozzáfűző (formátum mpd-idő-csf =) után a "/ manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Tartalom lejátszása
A videótovábbításhoz használja az [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) lejátszót.

Progresszív letöltés teszteléséhez illessze be egy URL-címet a böngészőjébe (például az Internet Explorer, az Chrome, a Safari).

## <a name="next-steps-media-services-learning-paths"></a>Következő lépések: Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
