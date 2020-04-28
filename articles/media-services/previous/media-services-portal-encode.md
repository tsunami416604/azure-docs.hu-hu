---
title: Eszköz kódolása a Azure Portal Media Encoder Standard használatával | Microsoft Docs
description: Ez az oktatóanyag végigvezeti egy eszköz kódolásának lépésein a Azure Portal Media Encoder Standard használatával.
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
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 7c147d99eaabee70316521d1f2bdc41933162ab1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "69542605"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Objektum kódolása a Media Encoder Standard használatával az Azure Portalon

> [!NOTE]
> Az oktatóanyag elvégzéséhez egy Azure-fiókra lesz szüksége. Részletekért lásd: az [Azure ingyenes próbaverziója](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

A Azure Media Services használata egyik leggyakoribb forgatókönyve az adaptív sávszélességű adatfolyam továbbítása az ügyfeleknek. Media Services a következő adaptív sávszélességű adatfolyam-technológiákat támogatja: Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming és dinamikus adaptív streaming HTTP-n keresztül (kötőjel, más néven MPEG-DASH). A videók adaptív sávszélességű adatfolyamként való előkészítéséhez először kódolja a forrás videóját többszörös sávszélességű fájlként. A videók kódolásához Media Encoder Standardt használhat.  

A Media Services biztosítja a dinamikus csomagolás lehetőségét. A dinamikus csomagolással a többszörös sávszélességű MP4 a HLS, a Smooth Streaming és az MPEG-DASH-ben is továbbíthatja anélkül, hogy újracsomagolást kellene elküldenie ezekben az adatfolyam-formátumokban. Ha dinamikus csomagolást használ, akkor a fájlok tárolása és kifizetése egytárolós formátumban is elvégezhető. A Media Services az ügyfél kérelme alapján építi fel és szolgálja ki a megfelelő választ.

Annak érdekében, hogy kihasználhassa a dinamikus csomagolást, kódolja többszörös sávszélességű MP4-fájlokká a forrásfájlt. A kódolási lépéseket a cikk későbbi részében mutatjuk be.

A médiafájlok feldolgozásának méretezésével kapcsolatos további információkért lásd: [a médiafájlok feldolgozásának skálázása a Azure Portal használatával](media-services-portal-scale-media-processing.md).

## <a name="encode-in-the-azure-portal"></a>Kódolás a Azure Portal

A tartalom kódolása Media Encoder Standard használatával:

1. Az [Azure-portálon](https://portal.azure.com/) válassza ki Azure Media Services-fiókját.
2. Válassza a **Beállítások** > **eszközök**elemet. Válassza ki a kódolni kívánt objektumot.
3. Válassza ki a **Kódolás** gombot.
4. Az **Objektum kódolása** ablakban válassza a **Media Encoder Standard** feldolgozóeszközt, és egy beállításkészletet. A beállításkészletekkel kapcsolatos információkért lásd a [sávszélességi skála automatikus létrehozását](media-services-autogen-bitrate-ladder-with-mes.md) és a [Media Encoder Standard előre beállított feladatait](media-services-mes-presets-overview.md) ismertető részeket. Fontos, hogy a bemeneti videóhoz legmegfelelőbb beállításkészlet legyen kiválasztva. Ha például tudja, hogy a bemeneti videó felbontása 1920 &#215; 1080 képpont, akkor választhatja a **H264 Multiple Bitrate 1080p** beállításkészletet. Ha a videó alacsony (640 &#215; 360) felbontású, ne használja a **H264 Multiple Bitrate 1080p** beállításkészletet.
   
   Az erőforrások egyszerűbb kezelése érdekében lehetősége van módosítani a kimeneti objektum nevét, illetve a feladat nevét.
   
   ![Objektumok kódolása](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Kattintson a **Létrehozás** gombra.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
* A [kódolási feladatok előrehaladásának figyelése](media-services-portal-check-job-progress.md) a Azure Portalban.  

