---
title: Adategység kódolása a Media Encoder Standard használatával az Azure Portalon |} A Microsoft Docs
description: Ez az oktatóanyag végigvezeti a lépéseken, egy eszköz kódolás a Media Encoder Standard használatával az Azure Portalon.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 90a94612248dead5b57ebff67562dda083d8669f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996134"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Adategység kódolása a Media Encoder Standard használatával az Azure Portalon

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További részletek: [Ingyenes Azure-próbafiók](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

A leggyakoribb forgatókönyvek az Azure Media Services az egyik forgatókönyve az adaptív sávszélességű streamelés az ügyfelek felé. A Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: Az Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming és dinamikus adaptív Streamelés (DASH, is néven MPEG-DASH) HTTP protokollon keresztül. Pedig előkészítheti a videók adaptív sávszélességű streamelés, először kódolnia a forrásvideókat többszörös sávszélességű MP4-fájlok formájában. Az Azure Media Encoder Standard használatával a videók kódolásához.  

A Media Services biztosítja a dinamikus csomagolás lehetőségét. A dinamikus csomagolás, a többszörös átviteli sebességű MP4-fájlnak a HLS, Smooth Streaming és MPEG-DASH, anélkül, hogy az ezekbe a formátumokba újracsomagolás juttathat el. Ha a dinamikus csomagolás használ, tárolja, és a fájlokat egyetlen tárolási formátumban kell fizetnie. A Media Services összeállítja és szolgálja ki a megfelelő választ az ügyfél által kért.

Annak érdekében, hogy kihasználhassa a dinamikus csomagolást, kódolja többszörös sávszélességű MP4-fájlokká a forrásfájlt. A kódolás lépéseit a cikk későbbi részében találja meg.

Méretezhető médiafeldolgozás kezelésével kapcsolatos információkért lásd: [méretezése az Azure portal használatával médiafeldolgozási](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Az Azure Portalon kódolása

Kódolás a Media Encoder Standard használatával:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Válassza a **Settgings (Beállítások)** > **Assets (Objektumok)** lehetőséget. Válassza ki a kódolni kívánt objektumot.
3. Válassza ki a **Kódolás** gombot.
4. Az **Objektum kódolása** ablakban válassza a **Media Encoder Standard** feldolgozóeszközt, és egy beállításkészletet. A beállításkészletekkel kapcsolatos információkért lásd a [sávszélességi skála automatikus létrehozását](media-services-autogen-bitrate-ladder-with-mes.md) és a [Media Encoder Standard előre beállított feladatait](media-services-mes-presets-overview.md) ismertető részeket. Fontos, hogy a bemeneti videóhoz legmegfelelőbb beállításkészlet legyen kiválasztva. Ha például tudja, hogy a bemeneti videó felbontása 1920 &#215; 1080 képpont, akkor választhatja a **H264 Multiple Bitrate 1080p** beállításkészletet. Ha a videó alacsony (640 &#215; 360) felbontású, ne használja a **H264 Multiple Bitrate 1080p** beállításkészletet.
   
   Az erőforrások egyszerűbb kezelése érdekében lehetősége van módosítani a kimeneti objektum nevét, illetve a feladat nevét.
   
   ![Objektumok kódolása](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Kattintson a **Létrehozás** gombra.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
* [A kódolási feladat előrehaladásának figyeléséhez](media-services-portal-check-job-progress.md) az Azure Portalon.  

