---
title: Fájlok feltöltése be egy Azure Media Services-fiókhoz használó többi |} Microsoft Docs
description: Útmutató a médiatartalom feltölti a Media Services létrehozásával és feltöltésével eszközök.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: juliako
ms.openlocfilehash: 1e51439ec0a6c6658b28ae0f02ff3eaeb4c551e4
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Fájlok feltöltése a Media Services-fiók használatával REST
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portál](media-services-portal-upload-files.md)
> 

A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. A [eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) entitás tartalmazhat videót, hang, képek, miniatűröket, szöveg nyomon követi és feliratfájlokat fájlokat (és a mindezen fájlok metaadatait.)  Ha a fájlok feltöltése az objektumba, a lesz biztonságosan tárolva a tartalom további feldolgozás és adatfolyam-felhő. 

Ebben az oktatóanyagban elsajátíthatja, hogyan töltse fel a fájl- és egyéb művelet:

> [!div class="checklist"]
> * A feltöltési művelet Postman beállítása
> * Kapcsolódás a Media Services szolgáltatáshoz 
> * Írási engedéllyel rendelkező hozzáférési házirend létrehozása
> * Egy eszköz létrehozása
> * Az SAS-kereső létrehozása és a feltöltés URL-cím létrehozása
> * Fájl feltöltése a blob storage a feltöltési URL-cím használatával
> * A metaadatok a médiafájl feltöltött eszköz létrehozása

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- [Az Azure portál használatával Azure Media Services-fiók létrehozása](media-services-portal-create-account.md).
- Tekintse át a [eléréséhez Azure Media Services API-t az AAD-hitelesítés áttekintése](media-services-use-aad-auth-to-access-ams-api.md) cikk.
- Konfigurálás **Postman** leírtak [konfigurálása Postman Media Services REST API-hívások](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Megfontolandó szempontok

Media Services REST API használatával az alábbiakat is érvényesek:
 
* Media Services REST API használatával entitások elérésekor be kell meghatározott fejlécmezők és értékek a HTTP-kérelmekre. További információkért lásd: [a Media Services REST API fejlesztési telepítő](media-services-rest-how-to-use.md). <br/>A jelen oktatóanyagban használt Postman gyűjtemény gondoskodik a szükséges fejlécek beállítása.
* A Media Services a IAssetFile.Name tulajdonság értékét használja, amikor az adatfolyam-tartalmak (például http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) a URL-címek kiépítéséhez Emiatt százalék-kódolás nem engedélyezett. Értékét a **neve** tulajdonság nem lehet a következő [százalék kódolás-fenntartott karakterek](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Emellett csak lehet egy "." a fájlnévkiterjesztés.
* A név hossza nem lehet hosszabb 260 karakternél.
* A Media Services által feldolgozható maximális támogatott fájlméret korlátozott. A fájlméretre vonatkozó korlátozással kapcsolatban további információt [ebben](media-services-quotas-and-limitations.md) a cikkben talál.

## <a name="set-up-postman"></a>A Postman beállítása

Ez az oktatóanyag Postman beállítása lépéseiért lásd: [konfigurálása Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

1. Kapcsolat értékek hozzá a környezethez. 

    Bizonyos változókat, amelyek részei a **MediaServices** [környezet](postman-environment.md) definiált műveletek végrehajtása előtt kézzel meg kell adnia a [gyűjtemény](postman-collection.md).

    Ahhoz, hogy az értékek az első öt változók, lásd: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-import-env.png)
2. Adja meg az értéket a **MediaFileName** környezeti változó.

    Adja meg az adathordozó azt tervezi, hogy töltse fel a fájl nevét. Ebben a példában fogjuk a BigBuckBunny.mp4 feltöltése. 
3. Vizsgálja meg a **AzureMediaServices.postman_environment.json** fájlt. Látni fogja, hogy szinte minden műveletet a gyűjtemény a "teszt" parancsfájl végrehajtása. A parancsfájlok igénybe vehet néhány érték, a válasz által visszaadott, és állítsa be a megfelelő környezeti változókat.

    Például az első művelet szolgáltatáshozzáférési tokent kap, és állítsa be a **AccessToken** környezeti változó, amely az összes többi műveletnél szolgál.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. A bal oldalon a **Postman** ablakban kattintson a **1. Az AAD-hitelesítési token beszerzése** -> **első Azure AD jogkivonat szolgáltatás egyszerű**.

    Az URL-cím részét a rendszer beírja a **AzureADSTSEndpoint** környezeti változó (az oktatóanyag a korábbi értékeinek beállítása [környezeti változók](#configure-the-environment) támogató a [gyűjtemény](#configure-the-collection)).

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postment-get-token.png)

5. Kattintson a **Küldés** gombra.

    A válasz, amely tartalmazza a "access_token" tekintheti meg. A "test" parancsfájl fogadja el ezt az értéket, és beállítja a **AccessToken** környezeti változó (a fentiekben ismertetett). Ha a környezeti változók megvizsgálja, látni fogja, hogy ez a változó most már tartalmazza a hozzáférési jogkivonat (tulajdonosi jogkivonattal) érték, amelynek a többi művelet. 

    Ha a jogkivonat lejár halad át a "Get Azure AD jogkivonat a szolgáltatás egyszerű" lépéssel újra. 

## <a name="create-an-access-policy-with-write-permission"></a>Írási engedéllyel rendelkező hozzáférési házirend létrehozása

### <a name="overview"></a>Áttekintés 

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

Fájlok feltöltése a blob-tárolóba, mielőtt írásra, hogy egy eszköz házirend jogosultságok a hozzáférés beállítása. Ehhez a AccessPolicies entitáskészlet HTTP-kérelmek POST. Adjon meg egy DurationInMinutes számot a létrehozása után, vagy egy belső kiszolgálót 500 hibaüzenetet kapja vissza válaszként. A AccessPolicies további információkért lásd: [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Hozzáférési házirend létrehozása

1. Válassza ki **AccessPolicy** -> **AccessPolicy létrehozása feltöltésének**.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-access-policy.png)

    A "test" parancsfájl AccessPolicy azonosítóját és a megfelelő környezeti változó beállítása.

## <a name="create-an-asset"></a>Egy eszköz létrehozása

### <a name="overview"></a>Áttekintés

Egy [eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) több típusok vagy a Media Services, beleértve a videó, hang, képeket, miniatűröket, szöveges nyomon követi és feliratfájlokat fájlok objektumainak tárolója. A REST API-ban az eszköz létrehozásához POST kérést küld a Media Services és az eszköz minden tulajdonságadatokat helyezi el a kérés törzsében.

A tulajdonságokat, amelyeket hozzáadhat egy eszköz létrehozásakor egyik **beállítások**. A következő titkosítási beállítások közül az egyik adható meg: **nincs** (alapértelmezés szerint nincs titkosítás használata) esetén **StorageEncrypted** (használja a tartalomhoz, amelyet a storage ügyféloldali titkosítás előzetes titkosítással), **CommonEncryptionProtected**, vagy **EnvelopeEncryptionProtected**. Ha egy titkosított eszköz, kell konfigurálni a továbbítási szabályzatban. További információkért lásd: [konfigurálása az adategység továbbítási házirendjeit](media-services-rest-configure-asset-delivery-policy.md).

Ha az eszköz titkosítva van, létre kell hoznia egy **ContentKey** , és az eszköz a következő cikkben leírtak szerint: [létrehozása egy ContentKey](media-services-rest-create-contentkey.md). A fájlok feltöltése az objektumba, után frissítenie kell a titkosítási tulajdonságok a a **AssetFile** entitás során kapott értékekkel a **eszköz** titkosítás. Használatával teheti a **EGYESÍTÉSE** HTTP-kérelem. 

Ebben a példában azt egy titkosítatlan eszköz létrehozásához. 

### <a name="create-an-asset"></a>Egy eszköz létrehozása

1. Válassza ki **eszközök** -> **eszköz létrehozása**.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-asset.png)

    A "test" parancsfájlt az eszköz azonosítója és a megfelelő környezeti változó beállítása.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Hozzon létre egy SAS-kereső és a feltöltése URL-cím létrehozása

### <a name="overview"></a>Áttekintés

Miután a AccessPolicy és lokátor beállítása, a rendszer a tényleges fájl feltöltése, egy Azure Blob Storage tárolóban, az Azure Storage REST API-k használatával. A fájlok blokkblobként kell feltöltenie. Lapblobokat nem Azure Media Services által támogatott.  

Az Azure storage blobs munkavégzés további információkért lásd: [Blob szolgáltatás REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

A tényleges feltöltés URL-címet kap, hozzon létre egy SAS-kereső (lásd alább). Keresők meghatározása a kezdési idő és a csatlakozási végpont típusú ügyfelek számára, szeretné, hogy egy eszköz lévő fájlok eléréséhez. Létrehozhat több lokátor entitás egy adott AccessPolicy és eszköz párhoz különböző ügyfélkérelmek és kell kezelni. A Lokátorokat mindegyikének használja a StartTime érték és a AccessPolicy DurationInMinutes értékének mennyi ideig egy URL-cím használható. További információkért lásd: [lokátor](https://docs.microsoft.com/rest/api/media/operations/locator).

A SAS URL-cím formátuma a következő:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe a következőket:

* Egy adott eszközhöz társított egyszerre legfeljebb öt egyedi keresők tartalmazhat. További információkért tekintse meg a lokátor.
* Ha szeretné azonnal töltse fel a fájlokat, akkor a StartTime érték az aktuális időpont előtt öt percet kell beállítania. Ennek az az oka lehet óra eltérésére az ügyfélszámítógép és a Media Services között. Ezenkívül a StartTime érték a következő dátum és idő formátumban kell lennie: éééé-hh-SSz (például "2014-05-23T17:53:50Z").    
* Előfordulhat, hogy a 30-40 második késleltetése, ha használható a lokátor létrehozása után.

### <a name="create-a-sas-locator"></a>Hozzon létre egy SAS-lokátor

1. Válassza ki **lokátor** -> **SAS-kereső létrehozása**.
2. Kattintson a **Küldés** gombra.

    A "test" parancsfájl létrehozza a "Feltöltés URL" a megadott adathordozó-fájl neve alapján, és a SAS-lokátor információkat, és beállítja a megfelelő környezeti változó.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Fájl feltöltése a blob storage a feltöltési URL-cím használatával

### <a name="overview"></a>Áttekintés

Most, hogy a feltöltési URL-címet, meg kell írnia egy kódrészletet az Azure Blob API-kkal közvetlenül fel kell töltenie a fájlt a SAS-tárolóhoz. További információkért tekintse át a következő cikkeket:

- [Az Azure Storage REST API használatával](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [HELYEZZE a Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Blobok feltöltése a Blob storage](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Postman rendelkező fájlt tölthet fel

Például a Postman a kis .mp4 fájl feltöltéséhez használjuk. Lehet egy fájl maximális mérete feltöltése bináris Postman keresztül.

A feltöltés kérelme, mert az nem része a **AzureMedia** gyűjtemény. 

Hozza létre, és állítson be egy új kérelmet:
1. Nyomja le az **+**, hogy hozzon létre egy új kérelmet a lapon.
2. Válassza ki **PUT** művelet és a Beillesztés **{{UploadURL}}** az URL-címben.
2. Hagyja **engedélyezési** lapon, mert a (számára ne adja meg azt a **tulajdonosi jogkivonat**).
3. Az a **fejlécek** lapra, adja meg: **kulcs**: "x-ms-blob-type" és **érték**: "BlockBlob".
2. Az a **törzs** lapra, majd **bináris**.
4. Válassza ki a megadott nevű fájlt a **MediaFileName** környezeti változó.
5. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Hozzon létre egy metaadat az eszköz

A fájl feltöltése után az eszköz a médiafájl az eszközhöz társított blob tárolóba feltöltött metaadat létrehozásához szükséges.

1. Válassza ki **AssetFiles** -> **CreateFileInfos**.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-file-info.png)

Fel kell tölteni a fájlt, és állítsa a metaadatait.

## <a name="validate"></a>Érvényesítés

Ellenőrzése, hogy a fájl sikeresen fel lett töltve, előfordulhat, hogy szeretné lekérdezni a [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) és hasonlítsa össze a **ContentFileSize** (vagy egyéb részletek) szolgáltatásokat az új eszköz jeleníteni. 

Például a következő **beolvasása** művelet fájl adatokat gyűjtenek a eszköz-fájl (a és a nagybetűk, a BigBuckBunny.mp4 fájlt). A lekérdezés használ a [környezeti változók](postman-environment.md) korábban beállított.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Válasz mérete, nevét és egyéb információkat fogja tartalmazni.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>További lépések

Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).

Emellett az Azure Functions használatával is elindíthatja a kódolási feladatokat a konfigurált tárolóba érkező fájlok alapján. További információkért tekintse meg [ezt a mintát](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

