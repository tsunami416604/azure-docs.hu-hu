---
title: Videofájlok streamelése az Azure Media Services használatával – .NET | Microsoft Docs
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
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 3834b4f07f6450f498831accfa6640f55bc5855a
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550178"
---
# <a name="tutorial-stream-video-files---net"></a>Oktatóanyag: Videófájlok streamelése – .NET

Ez az oktatóanyag bemutatja, hogy mennyire egyszerű is kódolása és videók a böngészők és eszközök az Azure Media Services számos streamelésének megkezdéséhez. A bemenő tartalmak HTTPS- URL- és SAS URL-címekkel vagy az Azure Blob Storage-ban található fájlok elérési útjával határozhatók meg.
A témakörben szereplő minta olyan tartalmakat kódol, amelyeket HTTPS URL-cím segítségével tehet elérhetővé. Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

Az oktatóanyag végén lesz a videó továbbításához.  

![Videó lejátszása](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nincs telepítve a Visual Studio, szerezze be a [Visual Studio Community 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15)-et.
- [A Media Services-fiók létrehozása](create-account-cli-how-to.md).<br/>Ellenőrizze, hogy ne felejtse el az értékeket, amelyeket meg az erőforráscsoport-nevet és a Media Services-fiók neve.
- Kövesse a [hozzáférés az Azure Media Services API az Azure CLI-vel](access-api-cli-how-to.md) és menteni a hitelesítő adatokat. Az API eléréséhez használandó kell.

## <a name="download-and-configure-the-sample"></a>Töltse le és a minta konfigurálásához

Klónozza a gépre a streamelési .NET-mintát tartalmazó GitHub-adattárat a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts.git
 ```

A minta az [EncodeAndStreamFiles](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/tree/master/AMSV3Quickstarts/EncodeAndStreamFiles) mappában található.

Nyissa meg [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/appsettings.json) a letöltött projektet. Cserélje le az értékeket a portáltól kapott hitelesítő adatokkal [API-k elérése](access-api-cli-how-to.md).

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy **átalakítása** (először ellenőrzi, hogy létezik-e a megadott átalakító). 
2. Létrehoz egy kimeneti **eszköz** használt, a kódolási **feladat**kimenetének.
3. Hozza létre a **feladat**bemeneti HTTPS URL-címet, amely alapján.
4. Elküldi a kódolás **feladat** a bemeneti és kimeneti korábban létrehozott használatával.
5. Ellenőrzi a feladat állapotát.
6. Létrehoz egy **lokátor**.
7. Streamelési URL-címeket épít fel.

A minta egyes funkcióinak leírásáért vizsgálja meg a kódot, és tekintse meg az [ebben a forrásfájlban](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) található megjegyzéseket.

## <a name="run-the-sample-app"></a>Mintaalkalmazás futtatása

Az alkalmazás futtatásakor megjelennek a videó különböző protokollokkal való lejátszására szolgáló URL-ek. 

1. Az *EncodeAndStreamFiles* alkalmazás futtatásához nyomja le a Ctrl+F5 billentyűkombinációt.
2. Válassza az Apple **HLS** protokollját (a vége *manifest(format=m3u8-aapl)*), és másolja a streamelési URL-címet a konzolról.

![Kimenet](./media/stream-files-tutorial-with-api/output.png)

A minta [forráskódjában](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) láthatja az URL felépítését. A létrehozásához össze kell fűznie a streamelési végpont gazdanevét és a streamelési lokátor elérési útját.  

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

## <a name="examine-the-code"></a>A kód vizsgálata

A minta egyes funkcióinak leírásáért vizsgálja meg a kódot, és tekintse meg az [ebben a forrásfájlban](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) található megjegyzéseket.

A [fájlok feltöltésével, kódolásával és streamelésével](stream-files-tutorial-with-api.md) foglalkozó oktatóanyag egy összetettebb streamelési példát biztosít részletesebb magyarázatokkal. 

### <a name="job-error-codes"></a>Feladathibakódok

Lásd: [hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="multithreading"></a>Több szál használata

Az Azure Media Services v3 SDK-k nem szálbiztosak. Többszálas alkalmazások használatakor minden szálhoz ajánlott létrehozni egy új AzureMediaServicesClient objektumot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: fájlok feltöltése, kódolása és streamelése](stream-files-tutorial-with-api.md)
