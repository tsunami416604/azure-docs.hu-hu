---
title: Fájlok feltöltése Azure Media Services-fiókba rest | Microsoft dokumentumok
description: Ismerje meg, hogyan tölthet be médiatartalmakat a Media Services szolgáltatásba eszközök létrehozásával és feltöltésével.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d5b84a9d216457720e9bd4e17b002d6ab9490f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888604"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Fájlok feltöltése Media Services-fiókba a REST használatával  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [Többi](media-services-rest-upload-files.md)
> * [Portál](media-services-portal-upload-files.md)
> 

A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. Az [Eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) entitás tartalmazhat video-, hang-, kép-, miniatűr gyűjtemények, szöveges pályák és feliratfájlok (és a metaadatok ezekről a fájlokat.)  Miután feltöltötte a fájlokat az eszközbe, a tartalom biztonságosan tárolódik a felhőben további feldolgozás és streamelés céljából. 

Ebben az oktatóanyagban megtudhatja, hogyan tölthet fel egy fájlt és a hozzá kapcsolódó egyéb műveleteket:

> [!div class="checklist"]
> * Postás beállítása az összes feltöltési művelethez
> * Kapcsolódás a Media Services szolgáltatáshoz 
> * Hozzáférési házirend létrehozása írási engedéllyel
> * Eszköz létrehozása
> * SAS-lokátor létrehozása és a feltöltési URL létrehozása
> * Fájl feltöltése a blobstorage-ba a feltöltési URL-cím használatával
> * Metaadatok létrehozása az eszközben a feltöltött médiafájlhoz

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mielőtt elkezdené.
- [Hozzon létre egy Azure Media Services-fiókot az Azure Portalon.](media-services-portal-create-account.md)
- Tekintse át az [Accessing Azure Media Services API-t Az AAD-hitelesítés áttekintése](media-services-use-aad-auth-to-access-ams-api.md) cikk.
- További információkért tekintse át az [Azure AD-hitelesítés használata a Media Services API-t rest-cikkel való eléréséhez.](https://docs.microsoft.com/azure/media-services/previous/media-services-rest-connect-with-aad)
- Konfigurálja **a Postmant** a [Postman konfigurálása a Media Services REST API-hívásokhoz című](media-rest-apis-with-postman.md)részében leírtak szerint.

## <a name="considerations"></a>Megfontolandó szempontok

A Media Services REST API használatakor a következő szempontok érvényesek:
 
* Ha a Media Services REST API-val rendelkező entitásokat fér hozzá, meghatározott fejlécmezőket és értékeket kell beállítania a HTTP-kérelmekben. További információt a [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md)című témakörben talál. <br/>Az oktatóanyagban használt Postman-gyűjtemény gondoskodik az összes szükséges fejléc beállításáról.
* A Media Services a IAssetFile.Name tulajdonság értékét használja a streamelési tartalom URL-címeinek létrehozásakor (például http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Ezért a százalékos kódolás nem engedélyezett. A **Name** tulajdonság értéke nem tartalmazhatja a következő [százalékkódolást fenntartott karaktereket](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$/?%#[]". A fájlnévkiterjesztéséhez csak egy "" lehet.
* A név hossza nem lehet hosszabb 260 karakternél.
* A Media Services által feldolgozható maximális támogatott fájlméret korlátozott. A fájlméretre vonatkozó korlátozással kapcsolatban további információt [ebben](media-services-quotas-and-limitations.md) a cikkben talál.

## <a name="set-up-postman"></a>A Postman beállítása

A Postman beállításának lépéseit az oktatóanyaghoz című témakörben [található.](media-rest-apis-with-postman.md)

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

1. Adja hozzá a kapcsolat értékeit a környezethez. 

    A **MediaServices** [környezet](postman-environment.md) részét játszó változókegy részét manuálisan kell beállítani a [gyűjteményben](postman-collection.md)definiált műveletek végrehajtásának megkezdése előtt.

    Az első öt változó értékeinek bekérése az [Azure Media Services API elérése Azure AD-hitelesítéssel című témakörben.](media-services-use-aad-auth-to-access-ams-api.md) 

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-import-env.png)
2. Adja meg a **MediaFileName** környezeti változó értékét.

    Adja meg a feltölteni kívánt adathordozó fájlnevét. Ebben a példában fogunk feltölteni a BigBuckBunny.mp4. 
3. Vizsgálja meg az **AzureMediaServices.postman_environment.json** fájlt. Látni fogja, hogy szinte minden művelet a gyűjteményben hajtson végre egy "teszt" parancsfájlt. A parancsfájlok a válasz által visszaadott értékeket veszik fel, és megfelelő környezeti változókat állítanak be.

    Például az első művelet kap egy hozzáférési jogkivonatot, és állítsa be az **AccessToken** környezeti változó, amely minden más műveletben használt.

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
4. A **Postman** ablak bal oldalán kattintson az **1-re. AAD hitelesítési token** -> **beszerzése Azure AD-token szolgáltatásnévhez.**

    Az URL-cím rész tele van az **AzureADSTSEndpoint** környezeti változóval (az oktatóanyag korábbi részében beállítja a gyűjteményt támogató környezeti változók értékeit).

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postment-get-token.png)

5. Kattintson a **Küldés** gombra.

    Láthatja a "access_token" tartalmazó választ. A "teszt" parancsfájl veszi ezt az értéket, és beállítja az **AccessToken** környezeti változó (a fent leírtak szerint). Ha megvizsgálja a környezeti változók, látni fogja, hogy ez a változó most már tartalmazza a hozzáférési jogkivonat (tulajdonosi jogkivonat) értéket, amely a többi művelet ben használt. 

    Ha a jogkivonat lejár, menjen át újra az "Azure AD-jogkivonat szolgáltatásért" lépés en. 

## <a name="create-an-access-policy-with-write-permission"></a>Hozzáférési házirend létrehozása írási engedéllyel

### <a name="overview"></a>Áttekintés 

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információt [ebben a cikkben](media-services-dotnet-manage-entities.md#limit-access-policies) talál.

Mielőtt bármilyen fájlt feltöltene a blobstorage-ba, állítsa be a hozzáférési szabályzat jogosultságait egy eszközíráshoz. Ehhez tegye közzé a HTTP-kérelmet az AccessPolicies entitáskészletnek. A létrehozáskor adjon meg egy DurationInMinutes értéket, vagy 500 belső kiszolgálói hibaüzenet jelenik meg válaszul. Az AccessPolicies programról további információt az [AccessPolicy című témakörben talál.](https://docs.microsoft.com/rest/api/media/operations/accesspolicy)

### <a name="create-an-access-policy"></a>Hozzáférési házirend létrehozása

1. Válassza **az AccessPolicy** -> **Create AccessPolicy for Upload**lehetőséget.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-access-policy.png)

    A "teszt" parancsfájl leadja az AccessPolicy azonosítót, és beállítja a megfelelő környezeti változót.

## <a name="create-an-asset"></a>Eszköz létrehozása

### <a name="overview"></a>Áttekintés

Az [eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) a Media Services többtípusú vagy objektumkészletének tárolója, beleértve a video-, hang-, kép- és miniatűrgyűjteményeket, szöveges sávokat és feliratfájlokat. A REST API-ban egy eszköz létrehozásához postai kérelmet kell küldeni a Media Servicesnek, és az eszközre vonatkozó bármely tulajdonságinformációt a kérelem törzsébe kell tenni.

Az eszköz létrehozásakor hozzáadható tulajdonságok egyike a **Beállítások**. A következő titkosítási beállítások közül választhat: **Nincs** (alapértelmezett, nincs titkosítás), **StorageEncrypted** (az ügyféloldali tárolótitkosítással előre titkosított tartalomhoz), **CommonEncryptionProtected**vagy **EnvelopeEncryptionProtected**. Ha titkosított eszközzel rendelkezik, konfigurálnia kell egy kézbesítési szabályzatot. További információt az [Eszközkézbesítési házirendek konfigurálása című témakörben talál.](media-services-rest-configure-asset-delivery-policy.md)

Ha az eszköz titkosítva van, létre kell hoznia egy **ContentKey-t,** és csatolnia kell az eszközhöz a következő cikkben leírtak szerint: [ContentKey létrehozása](media-services-rest-create-contentkey.md). Miután feltöltötte a fájlokat az eszközbe, frissítenie kell az **AssetFile** entitás titkosítási tulajdonságait az **eszköztitkosítás** során kapott értékekkel. Ehhez használja a **MERGE** HTTP kérést. 

Ebben a példában egy titkosítatlan eszközt hozunk létre. 

### <a name="create-an-asset"></a>Eszköz létrehozása

1. Válassza **ki az Eszközök** -> **létrehozása eszköz lehetőséget.**
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-asset.png)

    A "teszt" parancsfájl leadja az eszközazonosítót, és beállítja a megfelelő környezeti változót.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>SAS-lokátor létrehozása és a feltöltési URL létrehozása

### <a name="overview"></a>Áttekintés

Miután beállította az AccessPolicy és a Locator készletet, a tényleges fájl feltöltése egy Azure Blob Storage-tárolóba az Azure Storage REST API-k használatával. A fájlokat blokkblobként kell feltöltenie. Az Azure Media Services nem támogatja a lapblobokat.  

Az Azure storage blobjainak használatával kapcsolatos további tudnivalókért olvassa el a [Blob Service REST API című témakört.](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API)

A tényleges feltöltési URL-cím fogadásához hozzon létre egy SAS-lokátort (lásd alább). A lokátorok határozzák meg a kapcsolat végpontjának kezdési idejét és típusát azon ügyfelek számára, akik egy eszközfájljait szeretnék elérni. Egy adott AccessPolicy és Eszközpárhoz több lokátorentitást is létrehozhat a különböző ügyfélkérelmek és igények kezeléséhez. Ezek a lokátorok mindegyike a StartTime-értéket és az AccessPolicy DurationInMinutes értékét használja az URL-cím használható időtartamának meghatározásához. További információ: [Locator](https://docs.microsoft.com/rest/api/media/operations/locator).

A SAS URL-cím formátuma a következő:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe a következőket:

* Egy adott eszközhöz egyszerre legfeljebb öt egyedi lokátor társítható. További információ: Locator.
* Ha azonnal fel kell töltenie a fájlokat, állítsa be a StartTime értékét öt perccel az aktuális idő előtt. Ennek az az oka, hogy az ügyfélgép és a Media Services között óraeltérés lehet. A StartTime-értéknek a következő DateTime formátumban kell lennie: YYYY-MM-DDTHH:mm:ssZ (például "2014-05-23T17:53:50Z").    
* Előfordulhat, hogy a lokátor létrehozása után 30–40 másodperces késleltetés történik, amikor az elérhetővé válik.

### <a name="create-a-sas-locator"></a>SAS-lokátor létrehozása

1. Válassza **a Lokátor** -> **Létrehozása SAS-lokátor**lehetőséget.
2. Kattintson a **Küldés** gombra.

    A "teszt" parancsfájl létrehozza az "URL feltöltése" a megadott médiafájl neve és a SAS lokátor adatait, és beállítja a megfelelő környezeti változót.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Fájl feltöltése a blobstorage-ba a feltöltési URL-cím használatával

### <a name="overview"></a>Áttekintés

Most, hogy rendelkezik a feltöltési URL-címet, meg kell írnia néhány kódot az Azure Blob API-k használatával közvetlenül a fájl feltöltéséhez a SAS-tárolóba. További információkért tekintse át a következő cikkeket:

- [Az Azure Storage REST API használata](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [TEGYE Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Blobok feltöltése a Blob storage-ba](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Fájl feltöltése a Postman segítségével

Például postán töltünk fel egy kis .mp4 fájlt. Lehet, hogy a fájl mérete korlátozza a bináris feltöltése a Postman.There may be a file size limit on uploading binary through Postman.

A feltöltési kérelem **AzureMedia** nem része az AzureMedia-gyűjteménynek. 

Új kérelem létrehozása és beállítása:
1. Új **+** kérelemlap létrehozásához nyomja meg a billentyűt.
2. Válassza **a PUT** művelet lehetőséget, és illessze be **a(z) {{UploadURL}}** elemet az URL-címbe.
2. Hagyja meg az **Engedélyezés** lapot a hogy van (ne állítsa a **tulajdonosi jogkivonatra**).
3. A **Fejlécek** lapon adja meg a következő **kulcsot:**"x-ms-blob-type" és **"Value**: "BlockBlob".
2. A **Törzs** lapon kattintson a **bináris gombra.**
4. Válassza ki a **MediaFileName** környezeti változóban megadott nevű fájlt.
5. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Metaadatok létrehozása az eszközben

A fájl feltöltése után létre kell hoznia egy metaadatokat az eszközben a médiafájl feltöltött az eszközhöz társított blob storage.

1. Válassza az **AssetFiles** -> **CreateFileInfos lehetőséget.**
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-file-info.png)

A fájlt fel kell tölteni, és a metaadat-készlet.

## <a name="validate"></a>Érvényesítés

Annak ellenőrzéséhez, hogy a fájl sikeresen lett-e feltöltve, érdemes lehet lekérdezni az [AssetFile fájlt,](https://docs.microsoft.com/rest/api/media/operations/assetfile) és összehasonlítani a **ContentFileSize-ot** (vagy egyéb részleteket) az új eszközben várhatóan látottakkal. 

Például a következő **GET** művelet fájladatokat hoz létre az eszközfájlhoz (vagy esetben a BigBuckBunny.mp4 fájlhoz). A lekérdezés a korábban beállított [környezeti változókat](postman-environment.md) használja.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

A válasz méretet, nevet és egyéb információkat tartalmaz.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>További lépések

Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).

Emellett az Azure Functions használatával is elindíthatja a kódolási feladatokat a konfigurált tárolóba érkező fájlok alapján. További információkért tekintse meg [ezt a mintát](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

