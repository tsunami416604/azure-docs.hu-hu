---
title: Az Azure Media Services – Node.js videofájlok Stream |} A Microsoft Docs
description: Kövesse az oktatóanyag egy új Azure Media Services-fiók létrehozásához, egy fájl kódolása és streamelése azt az Azure Media Player.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: 3e4172cd149726e28e0c7dff435ec1f7a59ee169
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550172"
---
# <a name="tutorial-stream-video-files---nodejs"></a>Oktatóanyag: Videófájlok streamelése – Node.js

Ez az oktatóanyag bemutatja, hogy mennyire egyszerű is kódolása és videók a böngészők és eszközök az Azure Media Services számos streamelésének megkezdéséhez. A bemenő tartalmak HTTPS- URL- és SAS URL-címekkel vagy az Azure Blob Storage-ban található fájlok elérési útjával határozhatók meg.

Ebben a cikkben a minta kódolja a tartalmat, hogy az URL-címének HTTPS-en keresztül elérhető. Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

Az oktatóanyag végén lesz a videó továbbításához.  

![Videó lejátszása](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Node.js](https://nodejs.org/en/download/) telepítése
- [A Media Services-fiók létrehozása](create-account-cli-how-to.md).<br/>Ellenőrizze, hogy ne felejtse el az értékeket, amelyeket meg az erőforráscsoport-nevet és a Media Services-fiók neve.
- Kövesse a [hozzáférés az Azure Media Services API az Azure CLI-vel](access-api-cli-how-to.md) és menteni a hitelesítő adatokat. Az API eléréséhez használandó kell.

## <a name="download-and-configure-the-sample"></a>Töltse le és a minta konfigurálásához

Klónozza a GitHub-adattár, amely tartalmazza a folyamatos átviteli Node.js-minta a gépre a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

A mintában található a [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) mappát.

Nyissa meg [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) a letöltött projektet. Cserélje le a `endpoint config` értékek portáltól kapott hitelesítő adatokkal [API-k elérése](access-api-cli-how-to.md).

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy **átalakítása** (először ellenőrzi, hogy létezik-e a megadott átalakító). 
2. Létrehoz egy kimeneti **eszköz** használt, a kódolási **feladat**kimenetének.
3. Hozza létre a **feladat**bemeneti HTTPS URL-címet, amely alapján.
4. Elküldi a kódolás **feladat** a bemeneti és kimeneti korábban létrehozott használatával.
5. Ellenőrzi a feladat állapotát.
6. Létrehoz egy **lokátor**.
7. Streamelési URL-címeket épít fel.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

1. Az alkalmazás kódolt fájlokat tölti le. Hozzon létre egy mappát, ahová a kimeneti fájlokat, és frissítse az értéket, a **e** változót a [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) fájlt.
1. Nyissa meg **parancssor**, tallózással keresse meg a minta könyvtárat, és hajtsa végre a következő parancsokat.

    ```
    npm install 
    node index.js
    ```

Miután elkészült, futó, kimenetnek kell megjelennie hasonló:

![Futtatás](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Tesztelés az Azure Media Player használatával

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. 

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy webböngészőt, majd navigáljon a következő helyre: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Az **URL** mezőbe illessze be az alkalmazás futtatásakor kapott streamelési URL-értékek egyikét. 
 
     A Dash, HLS, beillesztheti az URL-címet, vagy Smooth formátum és az Azure Media Player átvált egy megfelelő streamelési protokoll az eszközre lejátszás céljából automatikusan.
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a az erőforrások az erőforráscsoportban, beleértve a Media Services és a storage-fiókok ebben az oktatóanyagban létrehozott törölje az erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Lásd még

[Feladat-hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Media Services – alapelvek](concepts-overview.md)
