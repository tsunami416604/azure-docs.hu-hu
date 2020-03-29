---
title: A rest használatával igény szerinti tartalom kézbesítése | Microsoft dokumentumok
description: Ez az oktatóanyag bemutatja az igény szerinti tartalomkézbesítési alkalmazás rest API-val történő megvalósításának lépéseit.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8989acc6d21a3c53be9d97c74ed7fbf03ba54819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773685"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Első lépések a tartalom igény szerinti kézbesítésével a REST használatával  

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Ez a rövid útmutató bemutatja az Azure Media Services (AMS) REST API-k használatával megvalósított video-on-demand (VoD) tartalomkézbesítési alkalmazás megvalósításának lépéseit.

Az útmutató bemutatja a Media Services alapvető munkafolyamatait és a Media Services-fejlesztéshez szükséges leggyakoribb programozási objektumokat és feladatokat. Az oktatóanyag befejezésekor streamelhet vagy fokozatosan letölthet egy feltöltött, kódolt és letöltött mintamédiafájlt.

A következő kép a Media Services OData-modellen alapuló VoD-alkalmazásfejlesztések során leggyakrabban használt objektumok közül mutat be néhányat.

Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Előfeltételek
A következő előfeltételek szükségesek a rest API-kkal rendelkező Media Services szolgáltatással való fejlesztés megkezdéséhez.

* Egy Azure-fiók. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Egy Media Services-fiók. A Media Services-fiók létrehozásáról a [Media Services-fiók létrehozása](media-services-portal-create-account.md) című cikk nyújt tájékoztatást.
* A Media Services REST API-val való fejlesztés ismertetése. További információt a [Media Services REST API – áttekintés című témakörben talál.](media-services-rest-how-to-use.md)
* Az Ön által választott alkalmazás, amely HTTP-kérelmeket és válaszokat küldhet. Ez a bemutató használ [Fiddler](https://www.telerik.com/download/fiddler).

A következő feladatok ebben a rövid útmutatóban jelennek meg.

1. A streamvégpont elindítása (az Azure Portal használatával).
2. Csatlakozzon a Media Services-fiókhoz a REST API-val.
3. Hozzon létre egy új eszközt, és töltsön fel egy videofájlt a REST API-val.
4. Kódolja a forrásfájlt adaptív sávszélességű MP4-fájlokba REST API-val.
5. Tegye közzé az eszközt, és kapjon streamelési és progresszív letöltési URL-eket a REST API-val.
6. Tartalom lejátszása

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Használja ugyanazt a házirend-azonosítót, ha mindig ugyanazokat a napokat / hozzáférési engedélyeket használja, például a helymeghatározókra vonatkozó házirendeket, amelyek hosszú ideig a helyükön maradnak (nem feltöltési házirendek). További információt [ebben a cikkben](media-services-dotnet-manage-entities.md#limit-access-policies) talál.

A cikkben használt AMS REST-entitásokról az [Azure Media Services REST API-referencia című témakörben olvashat részletesen.](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference) Lásd még az [Azure Media Services fogalmait.](media-services-concepts.md)

>[!NOTE]
>Amikor a Media Services entitásait éri el, meghatározott fejlécmezőket és értékeket kell beállítania a HTTP-kérelmekben. További információt a [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md)című témakörben talál.

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>A streamvégpont elindítása az Azure Portal használatával

Az Azure Media Services használatával való munka során az egyik leggyakoribb forgatókönyv a videó adaptív sávszélességű streamelésen keresztül történő kézbesítése. A Media Services dinamikus csomagolást biztosít, amelynek köszönhetően adaptív sávszélességű, MP4 formátumban kódolt tartalmait a Media Services által támogatott streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming) továbbíthatja igény szerint, mindezt anélkül, hogy az adott formátumban előcsomagolt verziót tárolna.

>[!NOTE]
>Amikor az AMS-fiók jön létre egy **alapértelmezett** streamelési végpont ot a fiók **leállított** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie.

A streamvégpont elindításához tegye a következőket:

1. Jelentkezzen be az [Azure Portalon.](https://portal.azure.com/)
2. Kattintson a Settings (Beállítások) ablak Streaming endpoints (Streamvégpontok) elemére.
3. Kattintson az alapértelmezett streamvégpontra.

    Megjelenik a DEFAULT STREAMING ENDPOINT DETAILS (Alapértelmezett streamvégpont adatai) ablak.

4. Kattintson a Start ikonra.
5. Mentse a módosításokat a Save (Mentés) gombra kattintva.

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>Csatlakozás a Media Services-fiókhoz REST API-val

Az AMS API-hoz való csatlakozásról az [Access the Azure Media Services API azure AD-hitelesítéssel című témakörben](media-services-use-aad-auth-to-access-ams-api.md)talál további információt. 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>Új eszköz létrehozása és videofájl feltöltése rest API-val

A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. Az **Eszköz** entitás tartalmazhat video-, hang-, kép-, miniatűr gyűjtemények, szöveges pályák és feliratfájlok (és a metaadatok ezekről a fájlokat.)  Miután feltöltötte a fájlokat az eszközbe, a tartalom biztonságosan tárolódik a felhőben további feldolgozás és streamelés céljából.

Az eszköz létrehozásakor megadandó értékek egyike az eszközlétrehozási beállítások. A **Beállítások** tulajdonság egy olyan felsorolási érték, amely leírja azokat a titkosítási beállításokat, amelyekkel egy eszköz létrehozható. Az érvényes érték az alábbi lista egyik értéke, nem pedig a lista értékeinek kombinációja:

* **Nincs** = **0** – nincs titkosítás. Ha ezt a beállítást használja, a tartalom nem védett az átvitel vagy a tárolóban lévő nyugalmi helyen.
    Ha egy MP4-fájlt progresszív letöltés útján tervez továbbítani, használja ezt a lehetőséget.
* **StorageEncrypted** = **1** – Titkosítja a tiszta tartalom helyileg AES-256 bites titkosítással, majd feltölti azt az Azure Storage, ahol tárolja titkosítva. A Storage-titkosítással védett adategységek titkosítása a kódolás előtt automatikusan fel lesz oldva, és egy titkosított fájlrendszerbe kerülnek; az új kimeneti adategységként való újbóli feltöltés előtt pedig lehetőség van az újbóli titkosításukra. A Storage-titkosítás elsősorban akkor hasznos, ha a kiváló minőségű bemeneti médiafájljait erős titkosítással szeretné védeni a lemezen való tároláskor.
* **CommonEncryptionProtected** = **2** - Akkor használja ezt a beállítást, ha olyan tartalmat tölt fel, amelymár titkosítva van és közös titkosítással vagy PlayReady DRM-mel védett (például PlayReady DRM-mel védett sima streamelés).
* **EnvelopeEncryptionProtected** = **4** – Akkor használja ezt az opciót, ha az AES-sel titkosított HLS-t tölti fel. A fájlokat a Transform Manager kódolhatja és titkosította.

### <a name="create-an-asset"></a>Eszköz létrehozása
Az eszköz a Media Services többtípusú vagy objektumkészletének tárolója, beleértve a video-, hang-, kép- és miniatűrgyűjteményeket, szöveges sávokat és feliratfájlokat. A REST API-ban egy eszköz létrehozásához postai kérelmet kell küldeni a Media Servicesnek, és az eszközre vonatkozó bármely tulajdonságinformációt a kérelem törzsébe kell tenni.

A következő példa bemutatja, hogyan hozhat létre egy eszközt.

**HTTP-kérés**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**HTTP-válasz**

Ha sikeres, a következő értéket adja vissza:

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

### <a name="create-an-assetfile"></a>Eszközfájl létrehozása
Az [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) entitás egy blobtárolóban tárolt video- vagy hangfájlt jelöl. Egy eszközfájl mindig egy eszközhöz van társítva, és egy eszköz egy vagy több AssetFiles-t tartalmazhat. A Media Services kódoló feladata sikertelen, ha egy eszközfájl-objektum nincs társítva egy blobtárolóban lévő digitális fájlhoz.

Miután feltöltötte a digitális médiafájlt egy blobtárolóba, a **MERGE** HTTP-kérelemmel frissíti az AssetFile fájlt a médiafájladataival (ahogy az a témakör későbbi részében látható).

**HTTP-kérés**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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


### <a name="creating-the-accesspolicy-with-write-permission"></a>Az AccessPolicy létrehozása írási engedéllyel
Mielőtt bármilyen fájlt feltöltene a blobstorage-ba, állítsa be a hozzáférési szabályzat jogosultságait egy eszközíráshoz. Ehhez tegye közzé a HTTP-kérelmet az AccessPolicies entitáskészletnek. A létrehozáskor adjon meg egy DurationInMinutes értéket, vagy 500 belső kiszolgálói hibaüzenet jelenik meg válaszul. Az AccessPolicies programról további információt az [AccessPolicy című témakörben talál.](https://docs.microsoft.com/rest/api/media/operations/accesspolicy)

A következő példa bemutatja, hogyan hozhat létre AccessPolicy-t:

**HTTP-kérés**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**HTTP-válasz**

Ha sikeres, a következő választ adja vissza:

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

### <a name="get-the-upload-url"></a>A feltöltési URL-cím beszerezni

A tényleges feltöltési URL-cím fogadásához hozzon létre egy SAS-lokátort. A lokátorok határozzák meg a kapcsolat végpontjának kezdési idejét és típusát azon ügyfelek számára, akik egy eszközfájljait szeretnék elérni. Egy adott AccessPolicy és Eszközpárhoz több lokátorentitást is létrehozhat a különböző ügyfélkérelmek és igények kezeléséhez. Ezek a lokátorok mindegyike a StartTime-értéket és az AccessPolicy DurationInMinutes értékét használja az URL-cím használható időtartamának meghatározásához. További információ: [Locator](https://docs.microsoft.com/rest/api/media/operations/locator).

A SAS URL-cím formátuma a következő:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Vegye figyelembe a következőket:

* Egy adott eszközhöz egyszerre legfeljebb öt egyedi lokátor társítható. 
* Ha azonnal fel kell töltenie a fájlokat, állítsa be a StartTime értékét öt perccel az aktuális idő előtt. Ennek az az oka, hogy az ügyfélgép és a Media Services között óraeltérés lehet. A StartTime-értéknek a következő DateTime formátumban kell lennie: YYYY-MM-DDTHH:mm:ssZ (például "2014-05-23T17:53:50Z").    
* Előfordulhat, hogy a lokátor létrehozása után 30–40 másodperces késleltetés történik, amikor az elérhetővé válik. Ez a probléma a [SAS URL-címére](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) és az Origin Lokátorokra egyaránt vonatkozik.

A következő példa bemutatja, hogyan hozhat létre egy SAS URL-lokátor, a kérelem törzsében a Type tulajdonság ("1" egy SAS-lokátor és "2" egy igény szerinti származási lokátor esetén) meghatározása szerint. A visszaadott **Path** tulajdonság tartalmazza a fájl feltöltéséhez használandó URL-címet.

**HTTP-kérés**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**HTTP-válasz**

Ha sikeres, a következő választ adja vissza:

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

### <a name="upload-a-file-into-a-blob-storage-container"></a>Fájl feltöltése blobtárolóba
Miután beállította az AccessPolicy és a Locator készletet, a tényleges fájl feltöltése egy Azure Blob storage-tárolóba az Azure Storage REST API-k használatával. A fájlokat blokkblobként kell feltöltenie. Az Azure Media Services nem támogatja a lapblobokat.  

> [!NOTE]
> Hozzá kell adnia a feltölteni kívánt fájl fájlnevét az előző szakaszban kapott Lokátor **elérési út** értékéhez. Például: `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Az Azure storage blobjainak használatával kapcsolatos további tudnivalókért olvassa el a [Blob Service REST API című témakört.](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)

### <a name="update-the-assetfile"></a>Az eszközfájl frissítése
Most, hogy feltöltötte a fájlt, frissítse a FileAsset-méret (és egyéb) adatokat. Példa:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-válasz**

Ha sikeres, a következő értéket adja vissza:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>A lokátor és az AccessPolicy törlése
**HTTP-kérés**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-válasz**

Ha sikeres, a következő értéket adja vissza:

    HTTP/1.1 204 No Content
    ...

**HTTP-kérés**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP-válasz**

Ha sikeres, a következő értéket adja vissza:

    HTTP/1.1 204 No Content
    ...

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>A forrásfájl kódolása adaptív sávszélességű MP4-fájlokba

Miután betárolta az eszközöket a Media Services szolgáltatásba, az adathordozók kódolhatók, transzmuxálhatók, vízjellel jelölhetők, és így tovább, mielőtt az ügyfeleknek kézbesítenék. Ezen tevékenységek több háttérbeli szerepkörpéldányhoz képest vannak ütemezve és futtatva a magas teljesítmény és rendelkezésre állás biztosítása érdekében. Ezeket a tevékenységeket feladatoknak nevezzük, és minden feladat atomi feladatokból áll, amelyek az eszközfájlon ténylegesen végzik a munkát (további információ: [Projekt](https://docs.microsoft.com/rest/api/media/operations/job), [Feladat](https://docs.microsoft.com/rest/api/media/operations/task) leírások).

Ahogy korábban említettük, az Azure Media Services használatával való munka során az egyik leggyakoribb forgatókönyv az adaptív sávszélességű streamelés az ügyfelek számára. A Media Services dinamikusan csomagolhat adaptív sávszélességű MP4-fájlokat a következő formátumok egyikébe: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

A következő szakasz bemutatja, hogyan hozhat létre egy feladatot, amely egy kódolási feladatot tartalmaz. A feladat határozza meg, hogy a mezzanine fájlt adaptív sávszélességű MP4-ekhalmazába kódolja át a **Media Encoder Standard**használatával. A szakasz azt is bemutatja, hogyan figyelheti a feladat feldolgozási folyamatát. Ha a feladat befejeződött, akkor képes lesz arra, hogy hozzon létre lokátorok, amelyek szükségesek az eszközök eléréséhez.

### <a name="get-a-media-processor"></a>Médiaprocesszor beszereznie
A Media Services szolgáltatásban a médiaprocesszor olyan összetevő, amely egy adott feldolgozási feladatot kezel, például kódolást, formátumátalakítást, titkosítást vagy visszafejtést. Az oktatóanyagban látható kódolási feladathoz a Media Encoder Standard-ot fogjuk használni.

A következő kód a kódoló azonosítóját kéri.

**HTTP-kérés**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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
Minden feladatnak lehet egy vagy több feladata a végrehajtani kívánt feldolgozás típusától függően. A REST API-n keresztül a feladatok és a hozzájuk kapcsolódó feladatok kétféleképpen hozhatnak létre: A feladatok szövegközien definiálhatók a Feladatok navigációs tulajdonságon keresztül a Feladat entitásokon, vagy az OData kötegelt feldolgozáson keresztül. A Media Services SDK kötegelt feldolgozást használ. A cikkben szereplő kódpéldák olvashatósága érdekében azonban a feladatok szövegközimeghatározásra kerülnek. A kötegelt feldolgozással kapcsolatos információkért lásd: [Open Data Protocol (OData) Batch Processing](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

A következő példa bemutatja, hogyan hozhat létre és könyvelheti fel azt a feladatot, amelyen egy feladat van beállítva, és hogy egy videót egy adott felbontásban és minőségben kódoljon. A következő dokumentációs szakasz a Media Encoder Standard processzor által támogatott összes [feladatkészlet](https://msdn.microsoft.com/library/mt269960) listáját tartalmazza.  

**HTTP-kérés**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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

Ha sikeres, a következő választ adja vissza:

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


Van néhány fontos dolog, amit figyelembe kell venni minden álláskérésben:

* A TaskBody tulajdonságoknak literális XML-t kell használniuk a feladat által használt bemeneti vagy kimeneti eszközök számának meghatározásához. A Feladat cikk az XML XML-sémadefinícióját tartalmazza.
* A TaskBody definícióban minden `<inputAsset>` belső `<outputAsset>` érték, és meg kell adni a JobInputAsset(érték) vagy JobOutputAsset(value).
* Egy feladatnak több kimeneti eszköze is lehet. Egy JobOutputAsset(x) csak egyszer használható egy feladat kimeneteként egy feladatban.
* Megadhatja jobinputasset vagy JobOutputAsset egy feladat bemeneti eszközeként.
* A feladatok nem képeznek ciklust.
* A JobInputAsset vagy JobOutputAsset értékparaméter egy eszköz indexértékét jelöli. A tényleges eszközök vannak definiálva az InputMediaAssets és OutputMediaAssets navigációs tulajdonságok a feladat entitás definíciója.

> [!NOTE]
> Mivel a Media Services az OData v3-ra épül, az InputMediaAssets és az OutputMediaAssets navigációs tulajdonsággyűjtemények egyes eszközeire egy "__metadata: uri" név-érték pár hivatkozik.
>
>

* Az InputMediaAssets leképezi a Media Services szolgáltatásban létrehozott egy vagy több eszközt. OutputMediaAssets jönlétre a rendszer. Nem hivatkoznak meglévő eszközre.
* A OutputMediaAssets az assetName attribútum használatával nevezhető el. Ha ez az attribútum nincs jelen, akkor az OutputMediaAsset neve az `<outputAsset>` elem belső szöveges értékétől független, vagy a Feladat név érték vagy a Feladatazonosító érték utótaggal (abban az esetben, ha a Name tulajdonság nincs definiálva). Ha például az assetName értékét "Minta" értékre állítja, akkor az OutputMediaAsset Name tulajdonság "Minta" lesz. Ha azonban nem állított be értéket az assetName értékhez, de a feladat nevét "NewJob" értékre állította, akkor az OutputMediaAsset name "JobOutputAsset(value)_NewJob" lesz.

    A következő példa bemutatja, hogyan állíthatja be az assetName attribútumot:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Feladatláncolás engedélyezése:

  * Egy feladatnak legalább két feladatból kell rendelkeznie
  * Legalább egy olyan feladatnak kell lennie, amelynek bemenete egy másik feladat kimenete a feladatban.

További információt a [Kódolási feladat létrehozása a Media Services REST API-val című témakörben talál.](media-services-rest-encode-asset.md)

### <a name="monitor-processing-progress"></a>Figyelési folyamat
A Feladat állapot az Állapot tulajdonság használatával érhető el, ahogy az a következő példában látható:

**HTTP-kérés**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**HTTP-válasz**

Ha sikeres, a következő választ adja vissza:

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


### <a name="cancel-a-job"></a>Feladat visszavonása
A Media Services lehetővé teszi a futó feladatok megszakítását a Feladat törlése funkción keresztül. Ez a hívás 400-as hibakódot ad vissza, ha egy feladat megszakításakor, megszakításakor, hibájaként vagy befejezésénél próbál meg szakítani egy feladatot.

A következő példa bemutatja, hogyan hívhatja meg a Feladat megszakítását.

**HTTP-kérés**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Ha sikeres, egy 204-es válaszkódot ad vissza az üzenet törzse nélkül.

> [!NOTE]
> A feladatazonosítót (általában nb:jid:UUID: somevalue) URL-kódolnikell, amikor a CancelJob paraméterként adja át.
>
>

### <a name="get-the-output-asset"></a>A kimeneti eszköz beszerzése
A következő kód bemutatja, hogyan lehet a kimeneti eszköz azonosítója.

**HTTP-kérés**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
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

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>Az eszköz közzététele és streamelési és progresszív letöltési URL-címek beszerzése rest API-val

Egy adategység továbbításához vagy letöltéséhez először a „közzététele” szükséges, egy kereső létrehozásával. Az objektumban található fájlokhoz a lokátorok biztosítanak hozzáférést. A Media Services kétféle keresőtípust támogat: az OnDemandOrigin keresők médiatartalmak továbbításához használatosak (például MPEG DASH, HLS vagy Smooth Streaming), a hozzáférési jogosultságkód (SAS)-keresők pedig médiafájlok letöltéséhez. 

Miután létrehozta a lokátorokat, létrehozhatja a fájlok streameléséhez vagy letöltéséhez használt URL-címeket.

>[!NOTE]
>Amikor az AMS-fiók jön létre egy **alapértelmezett** streamelési végpont ot a fiók **leállított** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie.

Egy Smooth Streaming URL-címnek a következő formátumban kell lennie:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Egy HLS-továbbítási URL-címnek a következő formátumban kell lennie:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Egy MPEG DASH-továbbítási URL-címnek a következő formátumban kell lennie:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Egy fájlok letöltéséhez használt SAS URL-címnek a következő formátumban kell lennie:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Ez a szakasz bemutatja, hogyan hajthatja végre az eszközök "közzétételéhez" szükséges következő feladatokat.  

* Az AccessPolicy létrehozása olvasási engedéllyel
* SAS-URL létrehozása tartalom letöltéséhez
* Élő tartalom forrásURL-jének létrehozása

### <a name="creating-the-accesspolicy-with-read-permission"></a>Az AccessPolicy létrehozása olvasási engedéllyel
A médiatartalmak letöltése vagy streamelése előtt először definiáljon olvasási engedélyekkel rendelkező AccessPolicy házirendet, és hozza létre a megfelelő Lokátor entitást, amely meghatározza az ügyfelek számára engedélyezni kívánt kézbesítési mechanizmus típusát. Az elérhető tulajdonságokról az [AccessPolicy entitás tulajdonságai című](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties)témakörben talál további információt.

A következő példa bemutatja, hogyan adható meg az AccessPolicy egy adott eszköz olvasási engedélyeit.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Ha sikeres, a rendszer egy 201-es sikereskódot ad vissza, amely leírja a létrehozott AccessPolicy entitást. Ezután az AccessPolicy-azonosítót a kézbesíteni kívánt fájlt (például kimeneti eszközt) tartalmazó eszköz eszközazonosítójával együtt a Lokátor entitás létrehozásához használja.

> [!NOTE]
> Ez az alapvető munkafolyamat ugyanaz, mint egy fájl feltöltése egy eszköz betöltésekénél (ahogy azt a témakör korábbi része is tárgyalta). A fájlok feltöltéséhez hasonlóan, ha Önnek (vagy az ügyfeleinek) azonnal hozzá kell férnie a fájlokhoz, állítsa a StartTime-értéket öt perccel az aktuális idő előtt. Erre a műveletre azért van szükség, mert lehet, hogy az ügyfél és a Media Services között óraeltérés van. A StartTime értéknek a következő DateTime formátumban kell lennie: YYYY-MM-DDTHH:mm:ssZ (például "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>SAS-URL létrehozása tartalom letöltéséhez
A következő kód bemutatja, hogyan szerezhet be egy URL-t, amely a korábban létrehozott és feltöltött médiafájl letöltésére használható. Az AccessPolicy olvasási engedélyekkel rendelkezik, és a lokátor elérési útja egy SAS letöltési URL-címre hivatkozik.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Ha sikeres, a következő választ adja vissza:

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

A visszaadott **elérési út** tulajdonság a SAS URL-címét tartalmazza.

> [!NOTE]
> Ha a tároló titkosított tartalmat tölti le, manuálisan vissza kell fejtenie a leadás előtt, vagy a Storage Decryption MediaProcessor-t egy feldolgozási feladatban a feldolgozott fájlok kimenetére a clear-ben egy OutputAsset-be, majd le kell töltenie az adott eszközről. A feldolgozásról további információt a Kódolási feladat létrehozása a Media Services REST API-val című témakörben talál. Emellett a SAS URL-lokátorok nem frissíthetők létrehozásuk után. Nem használhatja fel például ugyanazt a lokátort egy frissített StartTime értékkel. Ennek oka a SAS-URL-ek létrehozásának módja. Ha egy lokátor lejárta után szeretne hozzáférni egy eszközhöz letöltésre, akkor újat kell létrehoznia egy új StartTime-mal.
>
>

### <a name="download-files"></a>Fájlok letöltése
Miután beállította az AccessPolicy és a Locator készletet, fájlokat tölthet le az Azure Storage REST API-k használatával.  

> [!NOTE]
> Hozzá kell adnia a letölteni kívánt fájl fájlnevét az előző szakaszban kapott Lokátor **elérési út** értékéhez. Például: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Az Azure storage blobjainak használatával kapcsolatos további tudnivalókért olvassa el a [Blob Service REST API című témakört.](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)

A korábban végrehajtott kódolási feladat (adaptive MP4-készletbe történő kódolás) eredményeként több MP4-fájlja van, amelyeket fokozatosan letölthet. Példa:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Streamelési URL létrehozása a folyamatos átvitelű tartalomhoz
A következő kód bemutatja, hogyan hozhat létre streamelési URL-lokátort:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Ha sikeres, a következő választ adja vissza:

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

A folyamatos streamelés imént imasdáig egy streamelési médialejátszóban a Path tulajdonságot hozzá kell fűznie a Smooth Streaming jegyzékfájl nevéhez, amelyet a "/manifest" követ.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

A HLS streameléséhez fűzz hozzá (format=m3u8-aapl) a "/manifest" után.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Az MPEG DASH streameléséhez fűzz hozzá (format=mpd-time-csf) a "/manifest" után.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a name="play-your-content"></a><a id="play"></a>Tartalom lejátszása
A videótovábbításhoz használja az [Azure Media Services Player](https://aka.ms/azuremediaplayer) lejátszót.

A progresszív letöltés teszteléséhez illessze be az URL-t egy böngészőbe (például IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Következő lépések: Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
