---
title: Videofájlok streamelése az Azure Media Services szolgáltatással – Node.js | Microsoft dokumentumok
description: Az oktatóanyag lépéseit követve hozzon létre egy új Azure Media Services-fiókot, kódtasson egy fájlt, és továbbítsa azt az Azure Media Playerbe.
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
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: fa9fbf3bac55ca0b26c3644b7f6818fa96088612
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "69639393"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Oktatóanyag: Egy távoli fájl kódolása URL alapján és a videó streamelése - Node.js

Ez az oktatóanyag bemutatja, milyen egyszerű az Azure Media Services használatával a legkülönbözőbb böngészőkben és eszközökön lévő videók kódolása és streamelése. A bemenő tartalmak HTTPS- URL- és SAS URL-címekkel vagy az Azure Blob Storage-ban található fájlok elérési útjával határozhatók meg.

A jelen cikkben szereplő minta olyan tartalmat kódol, amelyet HTTPS-URL-címsegítségével tesz elérhetővé. Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

Végére a bemutató lesz képes-hoz patak egy videót.  

![Videó lejátszása](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Node.js](https://nodejs.org/en/download/) telepítése
- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md).<br/>Ügyeljen arra, hogy emlékezzen az erőforráscsoport nevéhez és a Media Services-fiók nevéhez használt értékekre.
- Kövesse az [Access Azure Media Services API-t az Azure CLI-vel,](access-api-cli-how-to.md) és mentse a hitelesítő adatokat. Az API eléréséhez használnia kell őket.

## <a name="download-and-configure-the-sample"></a>A minta letöltése és konfigurálása

Klónozzon egy GitHub-tárházat, amely a streamelési Node.js mintát tartalmazza a gépre a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

A minta a [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) mappában található.

Nyissa meg [az index.js fájlt](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L25) a letöltött projektben. Cserélje `endpoint config` le az értékeket az [API-k eléréséből](access-api-cli-how-to.md)származó hitelesítő adatokkal.

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy **átalakítást** (először ellenőrzi, hogy a megadott átalakítás létezik-e). 
2. Létrehoz egy kimeneti **eszközt,** amely a kódolási **feladat**kimeneteként használatos.
3. Https URL-cím alapján hozza létre a **feladat**bemenetét.
4. Elküldi a kódolási **feladat** a korábban létrehozott bemeneti és kimeneti használatával.
5. Ellenőrzi a feladat állapotát.
6. Létrehoz egy **streamelési lokátort.**
7. Streamelési URL-címeket épít fel.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

1. Az alkalmazás letölti a kódolt fájlokat. Hozzon létre egy mappát, ahová a kimeneti fájlokat el szeretné helyezni, és frissíteni szeretné az [index.js](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L39) fájl **kimenetimappa** változójának értékét.
1. Nyissa meg **a parancssort,** keresse meg a minta könyvtárát, és hajtsa végre a következő parancsokat.

    ```
    npm install 
    node index.js
    ```

Miután végzett a futás, meg kell látni a hasonló kimenet:

![Futtassa a következőt:](./media/stream-files-nodejs-quickstart/run.png)

## <a name="test-with-azure-media-player"></a>Tesztelés az Azure Media Player használatával

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. 

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/)webböngészőt, és keresse meg a ját.
2. Az **URL** mezőbe illessze be az alkalmazás futtatásakor kapott streamelési URL-értékek egyikét. 
 
     Az URL-címet HLS, Dash vagy Smooth formátumba illesztheti be, és az Azure Media Player automatikusan átvált a megfelelő streamelési protokollra az eszközön való lejátszáshoz.
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoport egyetlen erőforrására sem, beleértve az oktatóanyaghoz létrehozott Media Services- és tárfiókokat, törölje az erőforráscsoportot.

Hajtsa végre a következő CLI parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="see-also"></a>Lásd még

[Feladat hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Media Services fogalmai](concepts-overview.md)
