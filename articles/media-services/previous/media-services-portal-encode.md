---
title: Egy eszköz kódolása a Media Encoder Standard Azure-portálon |} Microsoft Docs
description: Ez az oktatóanyag végigvezeti a kódolási eszközként az Azure-portálon a Media Encoder Standard használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 470eb8613416f441c1becee628acf3c898591c84
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790222"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Egy eszköz kódolása a Media Encoder Standard Azure-portálon

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. További részletek: [Ingyenes Azure-próbafiók](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

A leggyakoribb forgatókönyve az, hogy működik-e az Azure Media Services egyik elkötelezett adaptív sávszélességű streamelés az ügyfelek számára. Media Services a következő adaptív sávszélességű streamelési technológiákat támogatja: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming és dinamikus adaptív adatfolyam-(kötőjel, más néven MPEG-DASH) http Protokollon keresztül. Az adaptív sávszélességű streamelés videók előkészítéséhez kódolás kimenete a forrás videó többszörös sávszélességű fájlként. Azure Media Encoder Standard segítségével a videók kódolásához.  

A Media Services biztosítja a dinamikus csomagolás lehetőségét. A dinamikus csomagolás biztosíthat a többszörös sávszélességű MP4 HLS, Smooth Streaming és MPEG-DASH, anélkül, hogy újból létre kell hozni az ezekbe a formátumokba. Dinamikus becsomagolás használatakor tárolhatnak, és a fájlok egyetlen-tárolási formátumban kell fizetnie. A Media Services alapszik, és betölti az ügyfél által kért a megfelelő választ.

Annak érdekében, hogy kihasználhassa a dinamikus csomagolást, kódolja többszörös sávszélességű MP4-fájlokká a forrásfájlt. A kódolás lépéseit egy a cikk későbbi részében.

Media feldolgozási méretezése kapcsolatban [skálázása az Azure portál használatával feldolgozása media](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Az Azure portálon kódolása

A tartalom kódolni Media Encoder Standard használatával:

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
* [A kódolási feladat előrehaladásának figyeléséhez](media-services-portal-check-job-progress.md) az Azure portálon.  

