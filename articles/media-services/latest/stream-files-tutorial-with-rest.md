---
title: Távoli fájl kódolása és streamelése az Azure Media Services v3-as használatával
description: Az oktatóanyag lépéseit követve kódoljon egy fájlt egy URL-cím alapján, és streamelje a tartalmat az Azure Media Services használatával a REST használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2020
ms.author: juliako
ms.openlocfilehash: 35be4ec2c4f5f8c299120c0ba7dbdcb1dd112473
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472033"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Oktatóanyag: Távoli fájl kódolása URL alapján és streamelés - REST

Az Azure Media Services lehetővé teszi, hogy médiafájljait olyan formátumokba kódolja, amelyek a legkülönbözőbb böngészőkben és eszközökön is lejátszhatók. Például előfordulhat, hogy az Apple HLS vagy MPEG DASH formátumában szeretné streamelni a tartalmakat. A streamelés előtt érdemes kódolni a jó minőségű digitális médiafájlokat. Kódolással kapcsolatos útmutatásért tekintse meg [a kódolás fogalmát](encoding-concept.md) ismertető cikket.

Ez az oktatóanyag bemutatja, hogyan kódolhatja a fájlt egy URL-cím alapján, és streamelheti a videót az Azure Media Services használatával REST használatával. 

![Videó lejátszása](./media/stream-files-tutorial-with-api/final-video.png)

Ez az oktatóanyag a következőket mutatja be:    

> [!div class="checklist"]
> * Media Services-fiók létrehozása
> * Hozzáférés a Media Services API-hoz
> * Postman-fájlok letöltése
> * Postman konfigurálása
> * Kérések küldése a Postman használatával
> * A streamelési URL-cím tesztelése
> * Az erőforrások eltávolítása

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md).

    Az erőforráscsoport nevéhez és a Media Services-fiók nevéhez használt értékek megjegyzése

- Telepítse a [Postman](https://www.getpostman.com/) REST-ügyfelet, hogy végrehajtsa az AMS REST oktatóanyagok egy részében látható REST API-kat. 

    A **Postmant** használjuk, de bármely egyéb REST-eszköz is megfelelő. Egyéb alternatívák: **Visual Studio Kód** a REST plugin vagy **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Postman-fájlok letöltése

Klónozzon egy GitHub-adattárat, amely tartalmazza a Postman-gyűjtemény és -környezet fájljait.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="access-api"></a>Hozzáférési API

Részletes információt a [Hitelesítő adatok beszedése a Media Services API eléréséhez című témakörben talál.](access-api-howto.md)

## <a name="configure-postman"></a>Postman konfigurálása

### <a name="configure-the-environment"></a>A környezet konfigurálása 

1. Nyissa meg a **Postman** alkalmazást.
2. A képernyő jobb oldalán válassza a **Manage environment (Környezet felügyelete)** lehetőséget.

    ![Környezet felügyelete](./media/develop-with-postman/postman-import-env.png)
4. A **Manage environment (Környezet felügyelete)** párbeszédablakban kattintson az **Import (Importálás)** gombra.
2. Keresse meg az `Azure Media Service v3 Environment.postman_environment.json` fájlt, amelyet a `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` klónozásakor töltött le.
6. Az **Azure Media Service v3 Environment** környezet hozzáadása megtörténik.

    > [!Note]
    > Frissítse a hozzáférési változókat a fenti **Hozzáférés a Media Services API-hoz** szakaszban található értékekkel.

7. Kattintson duplán a kiválasztott fájlra, és írja be az [API elérésének](#access-api) lépéseiben lekért értékeket.
8. Zárja be a párbeszédpanelt.
9. Válassza az **Azure Media Service v3 Environment** környezetet a legördülő menüből.

    ![Környezet kiválasztása](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>A gyűjtemény konfigurálása

1. Kattintson az **Import (Importálás)** gombra a gyűjteményfájl importálásához.
1. Keresse meg a `Media Services v3.postman_collection.json` fájlt, amelyet a `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` klónozásakor töltött le
3. Válassza a **Media Services v3.postman_collection.json** fájlt.

    ![Fájl importálása](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Kérések küldése a Postman használatával

Ebben a szakaszban olyan kéréseket küldünk, amelyek a kódolás és az URL-ek létrehozása szempontjából fontosak, hogy streamelhesse a fájlját. Konkrétan a következő kéréseket küldi el a rendszer:

1. Azure AD-jogkivonat lekérése a szolgáltatásnév hitelesítéséhez
1. Streamelési végpont indítása
2. Kimeneti objektum létrehozása
3. Átalakítás létrehozása
4. Feladat létrehozása
5. Streamelési lokátor létrehozása
6. A streamelési lokátor elérési útjainak listázása

> [!Note]
>  Ez az oktatóanyag azt feltételezi, hogy az összes erőforrást egyedi névvel hozza létre.  

### <a name="get-azure-ad-token"></a>Azure AD-jogkivonat lekérése 

1. A Postman alkalmazás bal oldali ablakában válassza az "1. lépés: AAD hitelesítéstoken beszereznie" lehetőséget.
2. Ezután válassza az „Get Azure AD Token for Service Principal Authentication” (Azure AD-jogkivonat lekérése egyszerű szolgáltatásnév hitelesítéséhez) lehetőséget.
3. Kattintson a **Küldés** gombra.

    A rendszer a következő **POST** műveletet küldi el.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A válasz a jogkivonattal együtt megérkezik, és beállítja az „AccessToken” környezeti változót a jogkivonat értékének. Az „AccessToken” értéket beállító kód megtekintéséhez kattintson a **Tests** (Tesztek) fülre. 

    ![AAD-jogkivonat lekérése](./media/develop-with-postman/postman-get-aad-auth-token.png)


### <a name="start-a-streaming-endpoint"></a>Streamelési végpont indítása

A streamelés engedélyezéséhez először el kell indítania azt a [streamelési végpontot,](https://docs.microsoft.com/azure/media-services/latest/streaming-endpoint-concept) amelyből a videót streamelni szeretné.

> [!NOTE]
> A számlázás csak akkor történik meg, ha a streamelési végpont futó állapotban van.

1. A Postman alkalmazás bal oldali ablakában válassza a "Streamelés és élő" lehetőséget.
2. Ezután válassza a "StreamingEndpoint indítása" lehetőséget.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **POST** műveletet küldi el:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/:streamingEndpointName/start?api-version={{api-version}}
        ```
    * Ha a kérelem sikeres, a `Status: 202 Accepted` rendszer visszaadja.

        Ez az állapot azt jelenti, hogy a kérelmet elfogadták feldolgozásra; a feldolgozás azonban nem fejeződött be. A `Azure-AsyncOperation` művelet állapotát a válaszfejléc értéke alapján kérdezheti le.

        Például a következő GET művelet adja vissza a művelet állapotát:
        
        `https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<resourceGroupName>/providers/Microsoft.Media/mediaservices/<accountName>/streamingendpointoperations/1be71957-4edc-4f3c-a29d-5c2777136a2e?api-version=2018-07-01`

        A [pálya aszinkron Azure-műveletek](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) cikk részletesen ismerteti, hogyan követheti nyomon az aszinkron Azure-műveletek állapotát a válaszban visszaadott értékeken keresztül.

### <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a kódolási feladat eredményeit. 

1. A Postman alkalmazás bal oldali ablakában válassza az "Eszközök" lehetőséget.
2. Ezután válassza a „Create or update an Asset” (Objektum létrehozása vagy frissítése) lehetőséget.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **PUT** műveletet küldi el:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * A művelet törzse a következő:

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID"
         }
        }
        ```

### <a name="create-a-transform"></a>Átalakítás létrehozása

A tartalmak Media Servicesben történő kódolása és feldolgozása során gyakran előfordul, hogy a kódolási beállításokat receptként adják meg. Ezután elküld egy **feladatot**, amely alkalmazza ezt a receptet egy videóra. Ha minden új videóhoz új állásokat küldesz be, akkor ezt a receptet a könyvtárad összes videójára alkalmazod. A Media Services esetében ezt a receptet **átalakításnak** nevezzük. További információt az [átalakításokkal és feladatokkal](transform-concept.md) kapcsolatos cikkben olvashat. Az ebben az oktatóanyagban leírt minta meghatároz egy receptet, amely elvégzi a videó kódolását, hogy azt streamelni lehessen többféle iOS- és Android-eszközre. 

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előzetes beállítás a bemeneti videót egy automatikusan létrehozott sávszélességi skálává (sávszélesség–felbontás párokká) kódolja a bemeneti felbontás és sávszélesség alapján, majd ISO MP4-fájlokat hoz létre H.264 kódolású video- és AAC kódolású audiosávokkal, amelyek megfelelnek a sávszélesség–felbontás pároknak. Az előzetes beállítással kapcsolatos információkért tekintse meg a [sávszélességi skálák automatikus létrehozását](autogen-bitrate-ladder.md) ismertető részt.

Használhatja a beépített EncoderNamedPreset beállítást vagy az egyéni előzetes beállításokat. 

> [!Note]
> [Átalakítások](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás. Ez az oktatóanyag azt feltételezi, hogy az átalakítást egyedi névvel hozza létre.

1. A Postman alkalmazás bal oldali ablakában válassza a "Kódolás és elemzés" lehetőséget.
2. Ezután kattintson a „Create Transform” (Átalakítás létrehozása) elemre.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **PUT** műveletet küldi el.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * A művelet törzse a következő:

        ```json
        {
            "properties": {
                "description": "Standard Transform using an Adaptive Streaming encoding preset from the library of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>Feladat létrehozása

A [feladat](https://docs.microsoft.com/rest/api/media/jobs) a tényleges kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét.

Ebben a példában a feladat bemenete egy HTTPS URL-címen alapul ("https:\//nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/").

1. A Postman alkalmazás bal oldali ablakában válassza a "Kódolás és elemzés" lehetőséget.
2. Ezután válassza a „Create or Update Job” (Feladat létrehozása vagy frissítése) lehetőséget.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **PUT** műveletet küldi el.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * A művelet törzse a következő:

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. A feladat előrehaladásának megtekintéséhez az Event Grid használatát javasoljuk. Ez egy magas rendelkezésre állású, egyenletes teljesítményű, dinamikusan skálázható szolgáltatás. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. Az egyszerű, HTTP-alapú reaktív eseménykezelés segít hatékony megoldásokat kialakítani az események intelligens szűrése és átirányítása révén.  További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

#### <a name="job-error-codes"></a>Feladathibakódok

Lásd: [Hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

### <a name="create-a-streaming-locator"></a>Streamelési lokátor létrehozása

A kódolási feladat befejezése után a következő lépés az, hogy a videó a kimeneti **eszköz** elérhetővé az ügyfelek számára a lejátszás. Ezt két lépésben teheti meg: először hozzon létre egy [StreamingLocatort](https://docs.microsoft.com/rest/api/media/streaminglocators), majd a streamelési URL-címeket, amelyeket az ügyfelek használhatnak. 

A streamelési lokátor létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint a streamelési lokátor az API-hívások kezdeményezése után azonnal érvényes, és a törlésig tart, kivéve, ha konfigurálja a választható kezdési és befejezési időpontokat. 

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása során meg kell adnia a kívánt **StreamingPolicyName** elemet. Ebben a példában a tiszta (vagy nem titkosított) tartalom streamelése lesz, így az előre definiált titkos adatfolyam-továbbítási házirend "Predefined_ClearStreamingOnly" használatos.

> [!IMPORTANT]
> Egyéni [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) használata esetén érdemes korlátozott számú szabályzatot létrehoznia a Media Service-fiókhoz, és újra felhasználni őket a StreamingLocator használatakor, amikor ugyanolyan titkosítási beállításokra és protokollokra van szükség. 

A Media Service-fiók rendelkezik kvótával a **streamelési szabályzat-bejegyzések** számára. Nem hozhat létre új **streamelési szabályzatot** minden egyes streamelési lokátorhoz.

1. A Postman alkalmazás bal oldali ablakában válassza a "Streamelési irányelvek és lokátorok" lehetőséget.
2. Ezután válassza a "Streamelési lokátor létrehozása (törlés)" lehetőséget.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **PUT** műveletet küldi el.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName?api-version={{api-version}}
        ```
    * A művelet törzse a következő:

        ```json
        {
          "properties": {
            "streamingPolicyName": "Predefined_ClearStreamingOnly",
            "assetName": "testAsset1",
            "contentKeys": [],
            "filters": []
         }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Elérési utak listázása és streamelési URL-ek létrehozása

#### <a name="list-paths"></a>Elérési utak listázása

Most, hogy létrejött a [streamelési lokátor,](https://docs.microsoft.com/rest/api/media/streaminglocators) lejuthat a streamelési URL-címekre

1. A Postman alkalmazás bal oldali ablakában válassza a "Streamelési irányelvek" lehetőséget.
2. Ezután válassza a „List Paths” (Elérési utak listázása) lehetőséget.
3. Kattintson a **Küldés** gombra.

    * A rendszer a következő **POST** műveletet küldi el.

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * A műveletnek nincs törzse:
        
4. Jegyezze fel a streameléshez használni kívánt elérési utak egyikét, amelyet a következő szakaszban fog használni. Ebben az esetben a következő elérési utak lettek visszaadva:
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>Streamelési URL-címek létrehozása

Ebben a szakaszban egy HLS-streamelési URL-címet hozunk létre. Az URL-címek a következő értékekből állnak:

1. A protokoll, amelyen keresztül a rendszer az adatokat küldi. Ebben az esetben „https”.

    > [!NOTE]
    > Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

2. A StreamingEndpoint gazdaneve. Ebben az esetben a név „amsaccount-usw22.streaming.media.azure.net”.

    Az állomásnév levételéhez a következő GET műveletet használhatja:
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingEndpoints/default?api-version={{api-version}}
    ```
    
3. Elérési út, amelyet az előző (Elérési utak listázása) szakaszban feljegyzett.  

Ennek eredményeként a következő HLS URL jött létre

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>A streamelési URL-cím tesztelése


> [!NOTE]
> Győződjön meg arról, hogy a **streamelési végpont,** amelyről streamelni szeretne, fut.

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. 

1. Nyisson meg egy [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)webböngészőt, és keresse meg a ját.
2. Az **URL:** mezőbe illessze be a létrehozott URL-t. 
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában mindent meg kell tisztítania, kivéve azokat az objektumokat, amelyeket újra fel kíván használni (általában újra fel használja **az Átalakításokat,** és megfogja tartani **a streamelési lokátorokat**stb.). Ha ki szeretné üríteni fiókját a kísérletezés után, töröljön minden erőforrást, amelyet nem szeretne ismét használni.  

Egy erőforrás törléséhez válassza a „Delete ...” (Törlés) műveletet a törölni kívánt erőforrás alatt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot.  

Hajtsa végre a következő CLI parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

Most, hogy már tudja, hogyan tölthet fel, kódolhat és streamelhet videókat, tekintse meg a következő cikket: 

> [!div class="nextstepaction"]
> [Videók elemzése](analyze-videos-tutorial-with-api.md)
