---
title: Feltöltés, kódolás és streamelés az Azure Media Serviceszel | Microsoft Docs
description: Az oktatóanyag lépéseit követve megtudhatja, hogyan tölthet fel egy fájlt, végezheti el egy videó kódolását, és streamelheti tartalmait az Azure Media Serviceszel REST használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 0faed5d72002f24d7be7602c5f16c18e66a0089e
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38308613"
---
# <a name="tutorial-upload-encode-and-stream-videos-with-rest"></a>Oktatóanyag: Videók feltöltése, kódolása és streamelése REST használatával

Az oktatóanyag bemutatja, hogyan tölthet fel, kódolhat és streamelhet videofájlokat az Azure Media Serviceszel.

A Media Services lehetővé teszi, hogy a médiafájlokat olyan formátumokká kódolja, amelyeket számos különféle böngészőben és eszközön le lehet játszani. Például előfordulhat, hogy az Apple HLS vagy MPEG DASH formátumában szeretné streamelni a tartalmakat. A streamelés előtt érdemes kódolni a jó minőségű digitális médiafájlokat. Kódolással kapcsolatos útmutatásért tekintse meg [a kódolás fogalmát](encoding-concept.md) ismertető cikket.

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

- Telepítse a [Postman](https://www.getpostman.com/) REST-ügyfelet, hogy végrehajtsa az AMS REST oktatóanyagok egy részében látható REST API-kat. 

    A **Postmant** használjuk, de bármely egyéb REST-eszköz is megfelelő. Egyéb alternatívák: **Visual Studio Code** REST beépülő modullal vagy **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Postman-fájlok letöltése

Klónozzon egy GitHub-adattárat, amely tartalmazza a Postman-gyűjtemény és -környezet fájljait.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="configure-postman"></a>Postman konfigurálása

Ebben a szakaszban konfiguráljuk a Postmant.

### <a name="configure-the-environment"></a>A környezet konfigurálása 

1. Nyissa meg a **Postmant**.
2. A képernyő jobb oldalán válassza a **Manage environment (Környezet felügyelete)** lehetőséget.

    ![Környezet felügyelete](./media/develop-with-postman/postman-import-env.png)
4. A **Manage environment (Környezet felügyelete)** párbeszédablakban kattintson az **Import (Importálás)** gombra.
2. Keresse meg az `Azure Media Service v3 Environment.postman_environment.json` fájlt, amelyet a `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` klónozásakor töltött le.
6. Az **Azure Media Service v3 Environment** környezet hozzáadása megtörténik.

    > [!Note]
    > Frissítse a hozzáférési változókat a fenti **Hozzáférés a Media Services API-hoz** szakaszban található értékekkel.

7. Zárja be a párbeszédpanelt.
8. Válassza az **Azure Media Service v3 Environment** környezetet a legördülő menüből.

    ![Környezet kiválasztása](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>A gyűjtemény konfigurálása

1. Kattintson az **Import (Importálás)** gombra a gyűjteményfájl importálásához.
1. Keresse meg a `Media Services v3 (2018-03-30-preview).postman_collection.json` fájlt, amelyet a `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` klónozásakor töltött le
3. Válassza a **Media Services v3 (2018-03-30-preview).postman_collection.json** fájlt.

    ![Fájl importálása](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Kérések küldése a Postman használatával

Ebben a szakaszban olyan kéréseket küldünk, amelyek a kódolás és az URL-ek létrehozása szempontjából fontosak, hogy streamelhesse a fájlját. Konkrétan a következő kéréseket küldi el a rendszer:

1. Azure AD-jogkivonat lekérése a szolgáltatásnév hitelesítéséhez
2. Kimeneti objektum létrehozása
3. Átalakítás létrehozása
4. Feladat létrehozása 
5. Streamelési lokátor létrehozása
6. A streamelési lokátorok elérési útjának listázása

> [!Note]
>  Ez az oktatóanyag azt feltételezi, hogy az összes erőforrást egyedi névvel hozza létre.  

### <a name="get-azure-ad-token"></a>Azure AD-jogkivonat lekérése 

1. A Postman bal ablakában válassza a „Step 1: Get AAD Auth token” (1. lépés: AAD-hitelesítési jogkivonat lekérése) lehetőséget.
2. Ezután válassza az „Get Azure AD Token for Service Principal Authentication” (Azure AD-jogkivonat lekérése egyszerű szolgáltatásnév hitelesítéséhez) lehetőséget.
3. Kattintson a **Küldés** gombra.

    A rendszer a következő **POST** műveletet küldi el.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. A válasz a jogkivonattal együtt megérkezik, és beállítja az „AccessToken” környezeti változót a jogkivonat értékének. Az „AccessToken” értéket beállító kód megtekintéséhez kattintson a **Tests** (Tesztek) fülre. 

    ![AAD-jogkivonat lekérése](./media/develop-with-postman/postman-get-aad-auth-token.png)

### <a name="create-an-output-asset"></a>Kimeneti objektum létrehozása

A kimeneti [objektum](https://docs.microsoft.com/rest/api/media/assets) tárolja a kódolási feladat eredményeit. 

1. A Postman bal ablakában válassza az „Assets” (Objektumok) lehetőséget.
2. Ezután válassza a „Create or update an Asset” (Objektum létrehozása vagy frissítése) lehetőséget.
3. Kattintson a **Küldés** gombra.

    A rendszer a következő **PUT** műveletet küldi el.

    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
    ```

### <a name="create-a-transform"></a>Átalakítás létrehozása

A tartalmak Media Servicesben történő kódolása és feldolgozása során gyakran előfordul, hogy a kódolási beállításokat receptként adják meg. Ezután elküld egy **feladatot**, amely alkalmazza ezt a receptet egy videóra. Ha minden egyes új videó esetében elküld egy új feladatot, ezt a receptet fogja alkalmazni a könyvtár összes videójára. A Media Services esetében ezt a receptet **átalakításnak** nevezzük. További információkat az [átalakításokkal és feladatokkal](transform-concept.md) kapcsolatos cikkben olvashat. Az ebben az oktatóanyagban leírt minta meghatároz egy receptet, amely elvégzi a videó kódolását, hogy azt streamelni lehessen többféle iOS- és Android-eszközre. 

Egy új [átalakításpéldány](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor meg kell adnia, milyen kimenetet szeretne létrehozni. A kötelező paraméter egy **TransformOutput** objektum. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. Az ebben a cikkben leírt minta az **AdaptiveStreaming** nevű beépített előzetes beállítást használja. Az előzetes beállítás a bemeneti videót egy automatikusan létrehozott sávszélességi skálává (sávszélesség–felbontás párokká) kódolja a bemeneti felbontás és sávszélesség alapján, majd ISO MP4-fájlokat hoz létre H.264 kódolású video- és AAC kódolású audiosávokkal, amelyek megfelelnek a sávszélesség–felbontás pároknak. Az előzetes beállítással kapcsolatos információkért tekintse meg a [sávszélességi skálák automatikus létrehozását](autogen-bitrate-ladder.md) ismertető részt.

Használhatja a beépített EncoderNamedPreset beállítást vagy az egyéni előzetes beállításokat. 

> [!Note]
> [Átalakítások](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás. Ez az oktatóanyag azt feltételezi, hogy az átalakítást egyedi névvel hozza létre.

1. A Postman bal ablakában válassza az „Encoding and Analysis” (Kódolás és elemzés) lehetőséget.
2. Ezután kattintson a „Create Transform” (Átalakítás létrehozása) elemre.
3. Kattintson a **Küldés** gombra.

    A rendszer a következő **PUT** műveletet küldi el.

    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
    ```

### <a name="create-a-job"></a>Feladat létrehozása

A [feladat](https://docs.microsoft.com/rest/api/media/jobs) a tényleges kérés a Media Services számára, hogy alkalmazza az adott **átalakítást** egy meghatározott bemeneti video- vagy audiotartalomra. A **feladat** meghatároz bizonyos adatokat, például a bemeneti videó és a kimenet helyét.

Ebben a példában a feladat bemenete egy HTTPS URL-címen („https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/”) alapul.

1. A Postman bal ablakában válassza az „Encoding and Analysis” (Kódolás és elemzés) lehetőséget.
2. Ezután válassza a „Create or Update Job” (Feladat létrehozása vagy frissítése) lehetőséget.
3. Kattintson a **Küldés** gombra.

    A rendszer a következő **PUT** műveletet küldi el.

    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```

A feladat végrehajtása némi időt vesz igénybe, és fontos, hogy értesüljön arról, ha ez megtörtént. A feladat előrehaladásának megtekintéséhez az Event Grid használatát javasoljuk. Ez egy magas rendelkezésre állású, egyenletes teljesítményű, dinamikusan skálázható szolgáltatás. Az Event Grid segítségével az alkalmazások figyelhetik gyakorlatilag az összes Azure-szolgáltatásból és az egyéni forrásokból származó eseményeket, és reagálhatnak azokra. Az egyszerű, HTTP-alapú reaktív eseménykezelés segít hatékony megoldásokat kialakítani az események intelligens szűrése és átirányítása révén.  További információkért tekintse meg az [események egyéni webes végponthoz való átirányítását](job-state-events-cli-how-to.md) ismertető cikket.

A **feladat** a következő állapotokon halad végig: **Ütemezve**, **Várólistán**, **Feldolgozás alatt**, **Befejeződött** (a végső állapot). Ha a feladat hibát észlelt, a **Hiba** állapot jelenik meg. Ha a feladat megszakítás alatt áll, a **Megszakítás**, a megszakítás befejeződése után pedig a **Megszakítva** állapot jelenik meg.

### <a name="create-a-streaming-locator"></a>Streamelési lokátor létrehozása

A kódolási feladat befejezése után a következő lépés a kimeneti objektumban található videó elérhetővé tétele az ügyfelek számára lejátszásra. Ezt két lépésben teheti meg: először hozzon létre egy [StreamingLocatort](https://docs.microsoft.com/rest/api/media/streaminglocators), majd a streamelési URL-címeket, amelyeket az ügyfelek használhatnak. 

A **StreamingLocator** létrehozásának folyamatát közzétételnek nevezzük. Alapértelmezés szerint a **StreamingLocator** azonnal érvényessé válik az API-hívás után, és a törléséig aktív marad. Ehelyett be lehet állítani indítási és befejeződési időpontokat is. 

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása során meg kell adnia a kívánt **StreamingPolicyName** elemet. Ebben a példában szabadon terjeszthető (vagy nem titkosított) tartalmat fog streamelni, így az előre beállított streamelési szabályzatot (**PredefinedStreamingPolicy.ClearStreamingOnly**) fogja használni.

> [!IMPORTANT]
> Egyéni [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) használata esetén érdemes korlátozott számú szabályzatot létrehoznia a Media Service-fiókhoz, és újra felhasználni őket a StreamingLocator használatakor, amikor ugyanolyan titkosítási beállításokra és protokollokra van szükség. 

A Media Service-fiókban korlátozva van a StreamingPolicy-bejegyzések száma. Nem érdemes új streamelési szabályzatot létrehozni minden egyes StreamingLocatorhöz.

1. A Postman bal ablakában válassza a „Streaming Policies” (Streamelési szabályzatok) lehetőséget.
2. Ezután válassza a „Create a Streaming Policy” (Streamelési szabályzat létrehozása) lehetőséget.
3. Kattintson a **Küldés** gombra.

    A rendszer a következő **PUT** műveletet küldi el.

    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingPolicies/:streamingPolicyName?api-version={{api-version}}
    ```

### <a name="list-paths-and-build-streaming-urls"></a>Elérési utak listázása és streamelési URL-ek létrehozása

#### <a name="list-paths"></a>Elérési utak listázása

A [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) létrehozása után lekérheti a streamelési URL-címeket

1. A Postman bal ablakában válassza a „Streaming Policies” (Streamelési szabályzatok) lehetőséget.
2. Ezután válassza a „List Paths” (Elérési utak listázása) lehetőséget.
3. Kattintson a **Küldés** gombra.

    A rendszer a következő **POST** műveletet küldi el.

    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
    ```
4. Jegyezze fel a streameléshez használni kívánt elérési utak egyikét, amelyet a következő szakaszban fog használni. Ebben az esetben a következő elérési utak lettek visszaadva:
    
    ```
    {
        "streamingPaths": [
            {
                "streamingProtocol": "Hls",
                "encryptionScheme": "NoEncryption",
                "paths": [
                    "/fd384f76-2d23-4e50-8fad-f9b3ebcd675b/Ignite-short.ism/manifest(format=m3u8-aapl)"
                ]
            },
            {
                "streamingProtocol": "Dash",
                "encryptionScheme": "NoEncryption",
                "paths": [
                    "/fd384f76-2d23-4e50-8fad-f9b3ebcd675b/Ignite-short.ism/manifest(format=mpd-time-csf)"
                ]
            },
            {
                "streamingProtocol": "SmoothStreaming",
                "encryptionScheme": "NoEncryption",
                "paths": [
                    "/fd384f76-2d23-4e50-8fad-f9b3ebcd675b/Ignite-short.ism/manifest"
                ]
            }
        ],
        "downloadPaths": []
    }
    ```

#### <a name="build-the-streaming-urls"></a>Streamelési URL-címek létrehozása

Ebben a szakaszban egy HLS-streamelési URL-címet hozunk létre. Az URL-címek a következő értékekből állnak:

1. A protokoll, amelyen keresztül a rendszer az adatokat küldi. Ebben az esetben „https”.

    > [!NOTE]
    > Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

2. A StreamingEndpoint gazdaneve. Ebben az esetben a név „amsaccount-usw22.streaming.media.azure.net”
3. Elérési út, amelyet az előző szakaszban feljegyzett.  

Ennek eredményeként a következő HLS URL jött létre

```
https://amsaccount-usw22.streaming.media.azure.net/fd384f76-2d23-4e50-8fad-f9b3ebcd675b/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>A streamelési URL-cím tesztelése

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. 

1. Nyisson meg egy webböngészőt, majd navigáljon a következő helyre: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Az **URL:** mezőbe illessze be a létrehozott URL-t. 
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="clean-up-resources-in-your-media-services-account"></a>A Media Service-fiók erőforrásainak eltávolítása

Általában érdemes eltávolítani mindent azon objektumok kivételével, amelyeket később is szeretne használni (átalakítások, StreamingLocator objektumok stb.). Ha ki szeretné üríteni fiókját a kísérletezés után, töröljön minden erőforrást, amelyet nem szeretne ismét használni.  

Egy erőforrás törléséhez válassza a „Delete ...” (Törlés) műveletet a törölni kívánt erőforrás alatt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportban lévő egyik erőforrásra sem, beleértve a jelen oktatóanyagban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott erőforráscsoportot. Ehhez használhatja a **CloudShell** eszközt.

Hajtsa végre az alábbi parancsot a **CloudShellben**:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>További lépések

Most, hogy már tudja, hogyan tölthet fel, kódolhat és streamelhet videókat, tekintse meg a következő cikket: 

> [!div class="nextstepaction"]
> [Videók elemzése](analyze-videos-tutorial-with-api.md)
