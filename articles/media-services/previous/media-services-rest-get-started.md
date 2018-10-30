---
title: Tartalmak továbbítása igény szerint REST használatával |} A Microsoft Docs
description: Ez az oktatóanyag végigvezeti a lépéseken egy on igény szerinti tartalomtovábbító alkalmazást az Azure Media Services REST API használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: fbfb52b758089acf65f89c62f716d46d62b0f289
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233820"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Tartalmak továbbítása igény szerint REST használatával
[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Ez a rövid útmutató végigvezeti a lépéseken, egy Video-on-Demand (VoD) tartalomtovábbító alkalmazást az Azure Media Services (AMS) REST API-k használatával.

Az útmutató bemutatja a Media Services alapvető munkafolyamatait és a Media Services-fejlesztéshez szükséges leggyakoribb programozási objektumokat és feladatokat. Az oktatóanyag befejezése után is tudja vagy fokozatosan letölteni egy, a feltöltött, kódolt és letöltött példa médiafájlt.

A következő kép a Media Services OData-modellen alapuló VoD-alkalmazásfejlesztések során leggyakrabban használt objektumok közül mutat be néhányat.

Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Előfeltételek
A Media Services REST API-kkal a fejlesztés megkezdése a következő előfeltételek szükségesek.

* Egy Azure-fiók. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Egy Media Services-fiók. A Media Services-fiók létrehozásáról a [Media Services-fiók létrehozása](media-services-portal-create-account.md) című cikk nyújt tájékoztatást.
* Megismerheti, hogyan hozhat létre Media Services REST API-val. További információkért lásd: [Media Services REST API – áttekintés](media-services-rest-how-to-use.md).
* A választott HTTP-kérések és válaszok küldő alkalmazás. Ebben az oktatóanyagban [Fiddler](http://www.telerik.com/download/fiddler).

Ebben a rövid útmutatóban a következő feladatok jelennek meg.

1. A streamvégpont elindítása (az Azure Portal használatával).
2. A Media Services-fiók REST API-val csatlakozhat.
3. Új eszköz létrehozása és a REST API-val videofájl feltöltése.
4. Forrásfájl kódolása adaptív sávszélességű MP4-fájlokká a REST API-val egy csoportba.
5. Tegye közzé az adategységet, és a streamelési és a progresszív letöltési URL-cím, a REST API-val.
6. Tartalom lejátszása

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Az ugyanazon házirend-azonosító akkor használja, ha Ön mindig ugyanazokat a napokat / hozzáférési engedélyeket, például olyan továbbra is helyben hosszú ideje (nem feltöltött szabályzatokat) keresők szabályzatai. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

A cikk ezt használja az AMS REST entitások kapcsolatos részletekért lásd: [Azure Media Services REST API-referencia](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Lásd még [Azure Media Services alapfogalmaiért](media-services-concepts.md).

>[!NOTE]
>A Media Services entitások elérésekor a be kell állítani a HTTP-kérelmekre a meghatározott fejlécmezők és értékek. További információkért lásd: [beállítása a Media Services REST API-k fejlesztését](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>A streamvégpont elindítása az Azure Portal használatával

Az Azure Media Services használatakor az egyik leggyakrabban használt funkciója a videók továbbítása az adaptív bitsebességű folyamatos átvitel. A Media Services dinamikus csomagolást biztosít, amelynek köszönhetően adaptív sávszélességű, MP4 formátumban kódolt tartalmait a Media Services által támogatott streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming) továbbíthatja igény szerint, mindezt anélkül, hogy az adott formátumban előcsomagolt verziót tárolna.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie.

A streamvégpont elindításához tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a Settings (Beállítások) ablak Streaming endpoints (Streamvégpontok) elemére.
3. Kattintson az alapértelmezett streamvégpontra.

    Megjelenik a DEFAULT STREAMING ENDPOINT DETAILS (Alapértelmezett streamvégpont adatai) ablak.

4. Kattintson a Start ikonra.
5. Mentse a módosításokat a Save (Mentés) gombra kattintva.

## <a id="connect"></a>Csatlakozás a Media Services-fiók REST API-val

Az AMS API-t kapcsolódás információkért lásd: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

## <a id="upload"></a>Új eszköz létrehozása és a REST API-val videofájl feltöltése

A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. A **eszköz** entitás videókhoz, audiofájlokhoz, képeket, miniatűröket, szöveges nyomon követi és akadálymentes felirat fájlokat (és mindezen fájlok metaadatait.) tartalmazhat.  A fájlok feltöltése az objektumba, miután a lesz biztonságosan tárolva a tartalom a felhőben további feldolgozás és streamelés céljából.

Az értékeket, amelyeket meg kell adnia, amikor egy eszköz létrehozása adategység-létrehozási lehetőségek közül. A **beállítások** tulajdonság egy enumerálási érték, amely leírja a titkosítási beállításokat, amelyek az eszköz lehet létrehozni. Érvényes értéket az alábbi listában, ez a lista értékeinek kombinációja nem szereplő értékek egyike:

* **Nincs** = **0** – nincs titkosítás. Ez a beállítás használatakor a tartalom nem védett átvitel, sem tárolás.
    Ha egy MP4-fájlt progresszív letöltés útján tervez továbbítani, használja ezt a lehetőséget.
* **StorageEncrypted** = **1** – a tiszta tartalom helyileg AES-256 bites titkosítást titkosítja, és ezután feltölti az Azure Storage helyén titkosítása. A Storage-titkosítással védett adategységek titkosítása a kódolás előtt automatikusan fel lesz oldva, és egy titkosított fájlrendszerbe kerülnek; az új kimeneti adategységként való újbóli feltöltés előtt pedig lehetőség van az újbóli titkosításukra. A Storage-titkosítás elsősorban akkor hasznos, ha a kiváló minőségű bemeneti médiafájljait erős titkosítással szeretné védeni a lemezen való tároláskor.
* **CommonEncryptionProtected** = **2** -használja ezt a beállítást, ha már titkosított és védett Common Encryption titkosítás és PlayReady DRM (például Smooth Streaming-tartalmat tölt fel védelem a PlayReady DRM technológiával).
* **EnvelopeEncryptionProtected** = **4** – használja ezt a beállítást, ha AES által titkosított HLS tölt fel. A fájlok kell kódolni és Transform Manager használatával titkosítja.

### <a name="create-an-asset"></a>Hozzon létre egy objektumot
Az eszköz egy olyan tároló, több típusok vagy a Media Services, beleértve a videót, hangot, képeket, miniatűröket, szövegsávok és feliratfájlok objektumok készleteit. A REST API-eszköz létrehozása szükséges POST kérést küld a Media Services és az eszköz semmilyen tulajdonságot információt elhelyezése a kérelem törzsében.

Az alábbi példa bemutatja, hogyan hozzon létre egy objektumot.

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

Ha ez sikeres, a következőket adja vissza:

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
A [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entitás a blobtárolóban tárolt video- és audiotartalmak fájlt jelöli. Egy eszköz fájl mindig egy eszköz társítva, és egy adategység tartalmazhat egy vagy több AssetFiles. A Media Services Encoder feladat sikertelen lesz, ha egy eszköz fájl objektumhoz nem kapcsolódik egy digitális fájlhoz a blobtárolóban.

A digitális média-fájl feltöltése a blob-tárolóba, után használja a **EGYESÍTÉSE** HTTP-kérelem a AssetFile frissítse a médiafájl információ (ahogyan a témakör későbbi részében látható).

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


### <a name="creating-the-accesspolicy-with-write-permission"></a>Az írási engedéllyel a AccessPolicy létrehozása
Fájlok feltöltése a blob storage-ba, mielőtt való íráshoz egy eszköz házirend jogok a hozzáférés beállítását. Ehhez a AccessPolicies entitáskészlet egy HTTP-kérelem KÜLDÉSE. Létrehozásakor DurationInMinutes érték megadása, vagy egy 500 belső hibaüzenetet kap vissza válaszként. AccessPolicies további információkért lásd: [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

Az alábbi példa bemutatja, hogyan hozhat létre egy AccessPolicy:

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

Ha ez sikeres, a következő választ adja vissza:

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

### <a name="get-the-upload-url"></a>A feltöltés URL-Címének lekéréséhez

A tényleges feltöltése URL-címet kap, hozzon létre egy SAS-kereső. Lokátorok a kezdési idő és a csatlakozási végpont típusa határozza meg az ügyfeleket, amelyek az eszköz lévő fájlok eléréséhez. Létrehozhat több keresőt entitás egy adott AccessPolicy és az Eszközintelligencia párhoz különböző ügyfélkérelmek képes kezelni az igényeinek megfelelően. Ezek a Lokátorokat mindegyike segítségével a StartTime értéke plusz a AccessPolicy DurationInMinutes értékét határozza meg, mennyi ideig egy URL-cím használható. További információkért lásd: [kereső](https://docs.microsoft.com/rest/api/media/operations/locator).

A SAS URL-cím formátuma a következő:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Vegye figyelembe a következőket:

* Nem lehet több mint öt, egy adott eszköz egyszerre társított egyedi keresők. 
* Ha szeretné azonnal a fájlok feltöltése, a StartTime érték öt perc alatt az aktuális időpont elé a kell beállítania. Ennek az oka lehet óra között az ügyfélszámítógép és a Media Services döntés. Ezenkívül a StartTime érték a következő dátum és idő formátumban kell lennie: éééé-hh-DDTHH:mm:ssZ (például "2014-05-23T17:53:50Z").    
* Előfordulhat, hogy egy 30 – 40 második késleltetés használatra elérhetővé válik egy kereső létrehozása után. A probléma egyaránt vonatkozik [SAS URL-címet](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) , a forrás-keresők.

Az alábbi példa bemutatja, hogyan hozhat létre egy SAS URL-cím kereső határozzák meg a kérelem törzsében ("1" egy SAS-kereső) és a egy igény szerinti forrás kereső "2" tulajdonság. A **elérési út** visszaadott tulajdonság tartalmazza az URL-cím, amely a fájl feltöltéséhez kell használnia.

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

Ha ez sikeres, a következő választ adja vissza:

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

### <a name="upload-a-file-into-a-blob-storage-container"></a>Fájl feltöltése a blob storage-tároló
Miután a AccessPolicy és kereső beállítása, a tényleges fájlt töltenek fel az Azure blob storage-tárolókat az Azure Storage REST API-k használatával. Blokkblobok formájában, fel kell tölteni a fájlokat. A lapblobok az Azure Media Services által nem támogatottak.  

> [!NOTE]
> A lokátor feltölteni kívánt fájl nevét hozzá kell adnia **elérési út** érték érkezett az előző szakaszban. Például: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .
>
>

Az Azure storage-blobokkal való használatáról további információkért lásd: [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Frissítés a AssetFile
Most, hogy a fájlt Ön által feltöltött, méretétől (és más) FileAsset információinak frissítéséhez. Példa:

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

Ha ez sikeres, a következőket adja vissza:

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

Ha ez sikeres, a következőket adja vissza:

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

Ha ez sikeres, a következőket adja vissza:

    HTTP/1.1 204 No Content
    ...

## <a id="encode"></a>Kódolja a forrásfájlt egy adaptív sávszélességű MP4-fájlok

Fürtjét a Media Services media szolgáltatásba eszközöket is kódolhatók, transmuxed, teljesítményjellemzőit, és így tovább, miután előtt biztosítását az ügyfeleknek. Ezen tevékenységek több háttérbeli szerepkörpéldányhoz képest vannak ütemezve és futtatva a magas teljesítmény és rendelkezésre állás biztosítása érdekében. Ezeket a tevékenységeket feladatoknak nevezzük, és minden feladat el a valódi munkát az adategységfájlon Részműveletből áll (további információkért lásd: [feladat](https://docs.microsoft.com/rest/api/media/operations/job), [feladat](https://docs.microsoft.com/rest/api/media/operations/task) leírása).

Ahogy korábban már említettük, ha az Azure Media Services egyik leggyakrabban használt működő forgatókönyve az adaptív sávszélességű streamelés az ügyfelek felé. A Media Services tudja dinamikusan csomagolni adaptív sávszélességű MP4-fájlokat a következő formátumok egyikét: HTTP Live Streaming (HLS), Smooth Streaming és MPEG DASH.

A következő szakasz bemutatja, hogyan hozhat létre egy feladatot, amely tartalmaz egy kódolási feladat. A feladat nem alakítható át mezzanine-fájlt adja meg az adaptív sávszélességű MP4-használatával egy csoportba **Media Encoder Standard**. A szakasz azt is bemutatja, hogyan figyelheti a feladat feldolgozása folyamatban van. Ha a feladat befejeződött, lenne az eszközök eléréséhez szükséges keresők létrehozása.

### <a name="get-a-media-processor"></a>Get-médiafeldolgozót.
A Media Services szolgáltatásban egy médiaprocesszorral egy összetevő, amely egy meghatározott feldolgozási feladatot, például a kódolás, formátumátalakítás, titkosításakor vagy visszafejtésekor médiatartalmak kezeli. A kódolási feladat jelenik meg ebben az oktatóanyagban fogjuk használni a Media Encoder Standard.

A következő kódot kér a kódoló azonosítója.

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
Minden egyes feladat elvégezni kívánt feldolgozási típusától függően egy vagy több feladat is rendelkezhet. A REST API-n keresztül hozhat létre feladatokat és azok kapcsolódó feladatok két módszer egyikével: feladatok lehetnek a tevékenységek navigációs tulajdonság entitások feladat vagy OData kötegelt feldolgozás révén definiálva van soron belül. A Media Services SDK-t használ a kötegelt feldolgozás. Az ebben a cikkben szereplő példák az olvashatóság érdekében, azonban a feladatok is definiálva van soron belül. Kötegelt feldolgozás kapcsolatos tudnivalókat lásd: [Open Data (OData) protokollnak kötegelt feldolgozási](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

Az alábbi példa bemutatja, hogyan hozhat létre, és a egy feladatot egy feladat beállítása egy adott feloldási és minőségi videó kódolásához közzététele. A következő dokumentáció tartalmazza az összes a [készletek feladat](http://msdn.microsoft.com/library/mt269960) a Media Encoder Standard-feldolgozó által támogatott.  

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

Ha ez sikeres, a következő választ adja vissza:

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


Van néhány fontos szempont, bármely feladathoz kérelem vegye figyelembe:

* TaskBody tulajdonságai szövegkonstans XML segítségével meghatározhatja a beviteli számát, vagy a tevékenység által használt eszközök kimeneti. A feladat a cikk az XML-séma definíció az XML-fájl tartalmazza.
* A TaskBody definíciójában minden egyes belső értékét <inputAsset> és <outputAsset> JobInputAsset(value) vagy JobOutputAsset(value) kell beállítani.
* Több kimeneti objektumok feladatonként. Egy JobOutputAsset(x) csak egyszer használhatók fel a feladat a feladat kimeneteként.
* Megadhatja a feladat bemeneti adategység JobInputAsset vagy JobOutputAsset.
* Feladatok kell nem alkotnak kört.
* A paraméter, amelyeket átad JobInputAsset vagy JobOutputAsset index értékét jelöli az adott eszköz számára. A tényleges eszközöket a feladat definícióját a InputMediaAssets és OutputMediaAssets navigációs tulajdonságok vannak meghatározva.

> [!NOTE]
> A Media Services OData v3 épül, mert az egyes eszközöket InputMediaAssets és OutputMediaAssets navigációs tulajdonság gyűjtemények a hivatkozott keresztül egy "__metadata: uri" név-érték pár.
>
>

* A Media Services szolgáltatásban létrehozott egy vagy több eszköz InputMediaAssets rendeli hozzá. A rendszer OutputMediaAssets hoznak létre. Egy meglévő eszköz akkor nem hivatkoznak.
* OutputMediaAssets elnevezheti a assetName attribútum használatával. Ha ez az attribútum nem szerepel, akkor a OutputMediaAsset neve, függetlenül a belső szöveges értéket a <outputAsset> elem van, a feladat név-érték, vagy a feladat azonosítóérték (abban az esetben, ha a Name tulajdonság nincs definiálva) utótaggal. Például ha assetName "Mintát" értéket, majd a OutputMediaAsset Name tulajdonság akkor állítható be "Mintaként". Azonban ha assetName értékét nem állította be, de adta meg az "NewJob" a feladat nevét, majd a OutputMediaAsset neve lesz "JobOutputAsset (érték) _NewJob".

    Az alábbi példa bemutatja, hogyan assetName attribútum:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Ahhoz, hogy a feladat-láncolás:

  * Egy feladat legalább két feladatot kell rendelkeznie.
  * Legalább egy tevékenységet, amelynek nincs egy másik feladat a feladat kimenetét kell lennie.

További információ: [kódolási feladat létrehozása a Media Services REST API-val](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>A figyelő a feldolgozása folyamatban van
Az állapot tulajdonság használatával lekérheti a feladat állapotát, az alábbi példában látható módon:

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

Ha ez sikeres, a következő választ adja vissza:

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
A Media Services lehetővé teszi, hogy megszakítja a futó feladatok CancelJob funkción keresztül. Ez a hívás egy 400-as hiba kód próbál egy feladat megszakítása, ha állapotában meg lett szakítva, megszakítása, hibát ad vissza, vagy befejeződött.

Az alábbi példa bemutatja, hogyan hívhat meg CancelJob.

**HTTP-kérelem**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Sikeres művelet esetén nem üzenettörzs a 204 válaszkódot adja vissza.

> [!NOTE]
> Meg kell URL-kódolása a feladatazonosító (általában nb:jid:UUID: érték1) számára történő átadásakor lévő, paraméterként CancelJob.
>
>

### <a name="get-the-output-asset"></a>A kimeneti objektum lekérése
A következő kód bemutatja, hogyan kérhetnek a kimeneti objektum azonosítóját.

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

## <a id="publish_get_urls"></a>Tegye közzé az adategységet, és a streamelési és a progresszív letöltési URL-cím, a REST API-val

Egy adategység továbbításához vagy letöltéséhez először a „közzététele” szükséges, egy kereső létrehozásával. A keresők biztosítják az adategységben található fájlokhoz való hozzáférést. A Media Services kétféle keresőtípust támogat: az OnDemandOrigin keresők médiatartalmak továbbításához használatosak (például MPEG DASH, HLS vagy Smooth Streaming), a hozzáférési jogosultságkód (SAS)-keresők pedig médiafájlok letöltéséhez. 

A keresők létrehozása után létrehozhatja a fájlok továbbításához vagy letöltéséhez használt URL-címeket.

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

Ez a szakasz bemutatja, hogyan hajtsa végre az alábbi feladatokat az eszközök "közzététele" szükséges.  

* Az olvasási engedéllyel rendelkező a AccessPolicy létrehozása
* Letölti a tartalmat hoz létre egy SAS URL-címe
* Forrás URL-címet hoz létre az online tartalomközvetítés

### <a name="creating-the-accesspolicy-with-read-permission"></a>Az olvasási engedéllyel rendelkező a AccessPolicy létrehozása
Letöltésével, illetve bármely médiafolyam előtt először egy olvasási jogosultsággal rendelkező AccessPolicy meghatározása és a megfelelő lokátor típusát adja meg az ügyfelek számára engedélyezni kívánja a kézbesítési mechanizmus entitás létrehozása. A rendelkezésre álló tulajdonságok további információkért lásd: [AccessPolicy entitás tulajdonságai](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

Az alábbi példa bemutatja, hogyan adja meg a AccessPolicy olvasási engedélyeket egy adott eszköz számára.

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

Ha ez sikeres, a 201 sikeres kódot ad vissza az Ön által létrehozott AccessPolicy entitást leíró. Ezt követően használhatja a AccessPolicy azonosítója, amely tartalmazza a fájl szeretne (például egy kimeneti objektum) létrehozása a lokátor entitás az eszköz az eszközazonosító mellett.

> [!NOTE]
> Ez a alapvető munkafolyamat megegyezik a fájlt feltölteni, amikor egy eszköz (mint hozzászóló, ez a témakör korábbi részében) fürtjét. Is például a fájlok feltöltése, ha Ön (vagy az ügyfelek) kell azonnal hozzáférhet a fájljaihoz, állítsa a StartTime értéket öt perc alatt az aktuális időpont elé. Ez a művelet szükség, mert előfordulhat óra között az ügyfél és a Media Services döntés. A StartTime érték a következő dátum és idő formátumban kell lennie: éééé-hh-DDTHH:mm:ssZ (például "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Letölti a tartalmat hoz létre egy SAS URL-címe
A következő kód bemutatja, hogyan hozhat létre, és korábban feltöltött adathordozó-fájl letöltésére használható URL-cím beszerzése. A AccessPolicy rendelkezik-e olvasási engedély, és a lokátor elérési út hivatkozik egy SAS-letöltési URL-címet.

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

Ha ez sikeres, a következő választ adja vissza:

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

A visszaadott **elérési út** tulajdonság tartalmazza az SAS URL-címet.

> [!NOTE]
> Tárolási titkosított tartalmat tölt le, ha, kell manuálisan megjelenítése, mielőtt visszafejteni, vagy a Storage visszafejtési MediaProcessor a segítségével egy feldolgozási feladat kimeneti feldolgozott fájlok a titkosítatlan egy OutputAsset, és töltse le az adott eszköz. A feldolgozási további információkért lásd: kódolási feladat létrehozása a Media Services REST API-val. SAS URL-cím keresők nem lehet frissíteni, miután létrejöttek. Például nem használhatja újra a frissített StartTime értékkel azonos lokátor. Ez a létrehozott SAS URL-címek módja miatt. Ha szeretne egy letölthető eszköz hozzá egy kereső lejárt, akkor egy új StartTime egy újat kell létrehoznia.
>
>

### <a name="download-files"></a>Fájlok letöltése
Miután a AccessPolicy és kereső beállítása, letöltheti a fájlt az Azure Storage REST API-k használatával.  

> [!NOTE]
> Töltse le a lokátor kívánt fájl nevét hozzá kell adnia **elérési út** érték érkezett az előző szakaszban. Például: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .
>
>

Az Azure storage-blobokkal való használatáról további információkért lásd: [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Eredményeként a kódolási feladat, amely a korábbi (kódolása az adaptív MP4 csoportba) végezte hogy több MP4-fájlokat fokozatosan lehet letölteni. Példa:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>A streamelési URL-cím hoz létre az online tartalomközvetítés
A következő kód bemutatja, hogyan URL-címet a streamelési Lokátorok létrehozásához:

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

Ha ez sikeres, a következő választ adja vissza:

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

Egy Smooth Streaming forrás URL-címet egy streamelési media Player streamelésére, hozzá kell fűzni a tulajdonság nevét a Smooth Streaming jegyzékfájl követ "/ jegyzékfájl" elérési útja.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Adatfolyam-HLS, a hozzáfűző (formátum = m3u8-aapl) után a "/ jegyzékfájl".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Adatfolyam-MPEG DASH, a hozzáfűző (formátum = mpd-time-csf) után a "/ jegyzékfájl".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Tartalom lejátszása
A videótovábbításhoz használja az [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) lejátszót.

Progresszív letöltés teszteléséhez, illessze be egy URL-címet egy böngészőben (például Internet Explorer, Chrome, a Safari).

## <a name="next-steps-media-services-learning-paths"></a>Következő lépések: Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
