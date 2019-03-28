---
title: Az Azure Media Services – Node.js videofájlok Stream |} A Microsoft Docs
description: Ezen rövid útmutató lépéseivel egy új Azure Media Services-fiókot hozhat létre, fájlt kódolhat, és streamelheti azt az Azure Media Playerbe.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: 22b7f2380b509daa4cb9931d6fc57c1297628e3d
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522855"
---
# <a name="quickstart-stream-video-files---nodejs"></a>Gyors útmutató: Stream-videó fájlok – Node.js

Ez a rövid útmutató bemutatja, hogy milyen könnyen kezdheti meg a videók kódolását és streamelését számos különféle böngészőben és eszközön az Azure Media Services használatával. A bemenő tartalmak HTTPS- URL- és SAS URL-címekkel vagy az Azure Blob Storage-ban található fájlok elérési útjával határozhatók meg.
A témakörben szereplő minta olyan tartalmakat kódol, amelyeket HTTPS URL-cím segítségével tehet elérhetővé. Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

A rövid útmutató végére képes lesz videók streamelésére.  

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

![Futtassa a következőt:](./media/stream-files-nodejs-quickstart/run.png)

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

Ha már nincs szüksége az erőforráscsoportjában lévő egyik erőforrásra sem, beleértve a jelen rövid útmutatóban létrehozott Media Services- és Storage-fiókokat, törölje a korábban létrehozott teljes erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Lásd még

[Feladat-hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Media Services – alapelvek](concepts-overview.md)
