---
title: Fájlok feltöltése Azure Media Services-fiók, REST használatával |} A Microsoft Docs
description: Megtudhatja, hogyan médiatartalmak kerülnek a Media Services létrehozása és feltöltése az eszközök által.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: dc9acae1e4d31e1456cd971fdab2745e7c58e910
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57892676"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Fájlok feltöltése Media Services-fiók REST használatával  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portál](media-services-portal-upload-files.md)
> 

A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. A [eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) entitás videókhoz, audiofájlokhoz, képeket, miniatűröket, szöveges nyomon követi és akadálymentes felirat fájlokat (és mindezen fájlok metaadatait.) tartalmazhat.  A fájlok feltöltése az objektumba, miután a lesz biztonságosan tárolva a tartalom a felhőben további feldolgozás és streamelés céljából. 

Ebben az oktatóanyagban elsajátíthatja, hogyan tölthet fel egy fájlt, és a hozzá társított más művelet:

> [!div class="checklist"]
> * A Postman beállítása az összes feltöltési műveletek esetében
> * Kapcsolódás a Media Services szolgáltatáshoz 
> * Írási engedéllyel rendelkező hozzáférési szabályzat létrehozása
> * Hozzon létre egy objektumot
> * Az SAS-keresők létrehozása és a feltöltése URL-cím létrehozása
> * Fájl feltöltése a blob storage a feltöltése URL-cím használatával
> * Hozzon létre egy metaadatok feltöltött media-fájl az eszközben

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- [Hozzon létre egy Azure Media Services-fiók az Azure portal használatával](media-services-portal-create-account.md).
- Tekintse át a [elérése az Azure Media Services API AAD-hitelesítés – áttekintés](media-services-use-aad-auth-to-access-ams-api.md) cikk.
- Konfigurálás **Postman** leírtak szerint [Postman konfigurálása a Media Services REST API-hívások](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Megfontolandó szempontok

A Media Services REST API használatával az alábbi szempontok érvényesek:
 
* Media Services REST API használatával való hozzáféréskor, be kell meghatározott fejlécmezők és értékeket a HTTP-kérelmekre. További információkért lásd: [beállítása a Media Services REST API-k fejlesztését](media-services-rest-how-to-use.md). <br/>A Postman-gyűjtemény, a jelen oktatóanyagban használt gondoskodik a szükséges fejlécek beállítása.
* A Media Services a IAssetFile.Name tulajdonság értékét használja, URL-címek létrehozását, a streamelési tartalom (például http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Ebből kifolyólag százalék-kódolást nem engedélyezett. Értékét a **neve** tulajdonság nem lehet a következő [százalék-kódolás – fenntartott karakterek](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? % # [] ". Emellett csak lehet egy "." a fájlnév kiterjesztésével.
* A név hossza nem lehet nagyobb, mint 260 karakter hosszúságú lehet.
* A Media Services által feldolgozható maximális támogatott fájlméret korlátozott. A fájlméretre vonatkozó korlátozással kapcsolatban további információt [ebben](media-services-quotas-and-limitations.md) a cikkben talál.

## <a name="set-up-postman"></a>A Postman beállítása

Ebben az oktatóanyagban a Postman beállítása lépéseiért lásd: [Postman konfigurálása](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

1. Csatlakozási értékek hozzá a környezethez. 

    Bizonyos változókat, amelyek részei a **MediaServices** [környezet](postman-environment.md) által meghatározott műveletek végrehajtása előtt manuálisan meg kell adnia a [gyűjtemény](postman-collection.md).

    Az első öt változók értékei lekéréséhez lásd: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-import-env.png)
2. Adja meg az értéket a **MediaFileName** környezeti változót.

    Adja meg az adathordozó kíván feltölteni a fájl nevét. Ebben a példában fogjuk a BigBuckBunny.mp4 feltöltése. 
3. Vizsgálja meg a **AzureMediaServices.postman_environment.json** fájlt. Látni fogja, hogy szinte minden művelet a gyűjteményben lévő egy "teszt" parancsfájl végrehajtása. A parancsfájlok igénybe vehet néhány értéket, a válasz által visszaadott, és állítsa be a megfelelő környezeti változókat.

    Például az első művelet lekérdezi egy hozzáférési jogkivonatot, és állítsa be a a **AccessToken** környezeti változót, amely minden egyéb művelet van használatban.

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
4. A bal oldalon, a **Postman** ablakban kattintson a **1. AAD-hitelesítési token beszerzése** -> **Azure AD Token letöltése az egyszerű szolgáltatás**.

    Az URL-cím része az ki van töltve a **AzureADSTSEndpoint** környezeti változót (az oktatóanyag korábbi részében meg, amelyek támogatják a gyűjtemény környezeti változók értékét).

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postment-get-token.png)

5. Kattintson a **Küldés** gombra.

    Láthatja, hogy a válasz, amely tartalmazza a "access_token". A "test" parancsfájlnak ezt az értéket, és beállítja a **AccessToken** (a fent ismertetett) környezeti változó. Ha megvizsgálja a környezeti változók, látni fogja, hogy ez a változó mostantól tartalmazza a hozzáférési jogkivonat (tulajdonosi jogkivonat) érték, amelynek a többi, a művelet. 

    Ha a jogkivonat lejár, próbálja ki az "Első Azure AD jogkivonat a szolgáltatás egyszerű" lépés újra. 

## <a name="create-an-access-policy-with-write-permission"></a>Írási engedéllyel rendelkező hozzáférési szabályzat létrehozása

### <a name="overview"></a>Áttekintés 

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

Fájlok feltöltése a blob storage-ba, mielőtt való íráshoz egy eszköz házirend jogok a hozzáférés beállítását. Ehhez a AccessPolicies entitáskészlet egy HTTP-kérelem KÜLDÉSE. Létrehozásakor DurationInMinutes érték megadása, vagy egy 500 belső hibaüzenetet kap vissza válaszként. AccessPolicies további információkért lásd: [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Hozzáférési szabályzat létrehozása

1. Válassza ki **AccessPolicy** -> **AccessPolicy létrehozása feltöltésének**.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-access-policy.png)

    A "test" parancsfájl AccessPolicy azonosítója lekérdezi és beállítja a megfelelő környezeti változót.

## <a name="create-an-asset"></a>Hozzon létre egy objektumot

### <a name="overview"></a>Áttekintés

Egy [eszköz](https://docs.microsoft.com/rest/api/media/operations/asset) egy tároló több típusok vagy a Media Services, beleértve a videót, hangot, képeket, miniatűröket, szövegsávok és feliratfájlok objektumok készleteit. A REST API-eszköz létrehozása szükséges POST kérést küld a Media Services és az eszköz semmilyen tulajdonságot információt elhelyezése a kérelem törzsében.

A tulajdonságokat, amelyeket hozzáadhat egy eszköz létrehozásakor egyik **beállítások**. Megadhatja a következő titkosítási beállítások egyikét: **Nincs** (alapértelmezés szerint titkosítás nem szerepel), **StorageEncrypted** (a tartalomhoz, amelynek már előre a storage ügyféloldali titkosítással titkosított), **CommonEncryptionProtected**, vagy  **EnvelopeEncryptionProtected**. Ha egy titkosított eszköz, kell egy objektumtovábbítási szabályzat konfigurálása. További információkért lásd: [adategység-kézbesítési házirendek konfigurálása](media-services-rest-configure-asset-delivery-policy.md).

Ha az eszköz titkosítva van, létre kell hoznia egy **ContentKey** hozzákapcsolja azt az objektumot a következő cikkben leírtak szerint: [Hogyan hozhat létre egy ContentKey](media-services-rest-create-contentkey.md). A fájlok feltöltése az objektumba, után frissítenie kell a titkosítás tulajdonságai a a **AssetFile** entitás során kapott értékekkel a **eszköz** titkosítást. Úgy teheti meg, hogy a **EGYESÍTÉSE** HTTP-kérés. 

Ebben a példában létrehozunk egy titkosítatlan eszköz. 

### <a name="create-an-asset"></a>Hozzon létre egy objektumot

1. Válassza ki **eszközök** -> **adategység létrehozása**.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-asset.png)

    A "test" parancsfájl beolvassa az eszköz azonosítója, és beállítja a megfelelő környezeti változót.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Az SAS-keresők létrehozása és feltöltése URL-cím létrehozása

### <a name="overview"></a>Áttekintés

Miután a AccessPolicy és kereső beállítása, a tényleges fájlt töltenek fel egy Azure Blob Storage-tárolóba az Azure Storage REST API-k használatával. Blokkblobok formájában, fel kell tölteni a fájlokat. A lapblobok az Azure Media Services által nem támogatottak.  

Az Azure storage-blobokkal való használatáról további információkért lásd: [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

A tényleges feltöltése URL-címet kap, hozzon létre egy SAS-kereső (lásd alább). Lokátorok a kezdési idő és a csatlakozási végpont típusa határozza meg az ügyfeleket, amelyek az eszköz lévő fájlok eléréséhez. Létrehozhat több keresőt entitás egy adott AccessPolicy és az Eszközintelligencia párhoz különböző ügyfélkérelmek képes kezelni az igényeinek megfelelően. Ezek a Lokátorokat mindegyike segítségével a StartTime értéke plusz a AccessPolicy DurationInMinutes értékét határozza meg, mennyi ideig egy URL-cím használható. További információkért lásd: [kereső](https://docs.microsoft.com/rest/api/media/operations/locator).

A SAS URL-cím formátuma a következő:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Megfontolandó szempontok

Vegye figyelembe a következőket:

* Nem lehet több mint öt, egy adott eszköz egyszerre társított egyedi keresők. További információkért lásd: Lokátort.
* Ha szeretné azonnal a fájlok feltöltése, a StartTime érték öt perc alatt az aktuális időpont elé a kell beállítania. Ennek az oka lehet óra között az ügyfélszámítógép és a Media Services döntés. Ezenkívül a StartTime érték a következő dátum és idő formátumban kell lennie: ÉÉÉÉ-hh-DDTHH:mm:ssZ (például "2014-05-23T17:53:50Z").    
* Előfordulhat, hogy egy 30 – 40 második késleltetés használatra elérhetővé válik egy kereső létrehozása után.

### <a name="create-a-sas-locator"></a>Hozzon létre egy SAS-lokátor

1. Válassza ki **kereső** -> **SAS-lokátor létrehozása**.
2. Kattintson a **Küldés** gombra.

    A "test" parancsfájl létrehozza a "Feltöltése URL-címe" a megadott adathordozó-fájl neve alapján, és a SAS-kereső adatokat, és beállítja a megfelelő környezeti változót.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Fájl feltöltése a blob storage a feltöltése URL-cím használatával

### <a name="overview"></a>Áttekintés

Most, hogy a feltöltése URL-címe van, írjon egy kódrészletet az Azure Blob API-k használatával közvetlenül a fájl feltöltéséhez a SAS-tárolóhoz szeretne. További információkért tekintse át a következő cikkeket:

- [Az Azure Storage REST API használatával](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Blob KIHELYEZÉSE](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Blobok feltöltése a Blob storage](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Töltsön fel egy fájlt a postman használatával

Tegyük fel a Postman feltölthet egy kis .mp4-fájlt használjuk. A Postman használatával bináris feltöltése előfordulhat, hogy lehet egy fájl maximális mérete.

A feltöltés kérelme, mert nem része a **AzureMedia** gyűjtemény. 

Hozzon létre, és a egy új kérelmet beállítása:
1. Nyomja meg **+**, hozhat létre egy új kérelmet lapon.
2. Válassza ki **PUT** művelet és a Beillesztés **{{UploadURL}}** URL-címét.
2. Hagyja **engedélyezési** lapra, a rendszer (nincs beállítva, a **tulajdonosi jogkivonat**).
3. Az a **fejlécek** lapra, adja meg: **Kulcs**: "x-ms-blob-type" és a **érték**: "BlockBlob".
2. Az a **törzs** lapra, majd **bináris**.
4. Válassza ki a megadott nevű fájlt a **MediaFileName** környezeti változót.
5. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Hozzon létre egy metaadatokat az eszközben

A fájl feltöltése után kell létrehozni egy metaadatait, az az eszközhöz társított blob storage-bA feltöltött media-fájl az eszközben.

1. Válassza ki **AssetFiles** -> **CreateFileInfos**.
2. Kattintson a **Küldés** gombra.

    ![Fájl feltöltése](./media/media-services-rest-upload-files/postman-create-file-info.png)

Fel kell tölteni a fájlt, és állítsa a metaadatait.

## <a name="validate"></a>Érvényesítés

Ellenőrzése, hogy a fájl sikeresen fel lett töltve, előfordulhat, hogy szeretné lekérdezni a [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) és hasonlítsa össze a **ContentFileSize** (vagy egyéb részletek) mi várható az új eszköz jelenik meg. 

Például a következő **LEKÉRÉSE** művelet elérhetővé teszi a fájladatok, az eszközintelligencia-fájl (a vagy megkülönbözteti a kis, a BigBuckBunny.mp4 fájl). A lekérdezés használ a [környezeti változók](postman-environment.md) korábban beállított.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Válasz mérete, neve és egyéb információkat tartalmazza.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>További lépések

Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).

Emellett az Azure Functions használatával is elindíthatja a kódolási feladatokat a konfigurált tárolóba érkező fájlok alapján. További információkért tekintse meg [ezt a mintát](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

