---
title: Részklip egy videót, amikor a kódolás az Azure Media Services REST API
description: Ez a témakör ismerteti, hogyan részklip egy videót, amikor a kódolás az Azure Media Serviceshez REST segítségével
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/10/2019
ms.author: juliako
ms.openlocfilehash: df8c8a4040b4aae4379b4bfe0e9a16337588dd1b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304895"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Részklip egy videót, amikor a kódolás a Media Services – REST

Trim vagy a videó részklip használatával kódolás esetén egy [feladat](https://docs.microsoft.com/rest/api/media/jobs). Ez a funkció együttműködik bármely [átalakítása](https://docs.microsoft.com/rest/api/media/transforms) épül fel, amelyek segítségével a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) készleteket, vagy a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) készletek. 

Ebben a témakörben a REST-példa létrehoz egy feladatot, amely egy videót levágja, a kódolási feladatokat a helyrendszerekre. 

## <a name="prerequisites"></a>Előfeltételek

Az ebben a témakörben leírt lépések elvégzéséhez kell:

- [Az Azure Media Services-fiók létrehozása](create-account-cli-how-to.md).
- [Postman konfigurálása az Azure Media Services REST API-hívások](media-rest-apis-with-postman.md).
    
    Kövesse a témakör az utolsó lépés [lekérése az Azure AD-Token](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Hozzon létre egy-egy átalakítási és a egy kimeneti objektumok. Láthatja, hogyan hozhat létre egy-egy átalakítási és a egy kimeneti az eszközök a [URL-cím alapján egy távoli fájl kódolása és streamelése a videó - REST](stream-files-tutorial-with-rest.md) oktatóanyag.
- Tekintse át a [kódolás fogalom](encoding-concept.md) témakör.

## <a name="create-a-subclipping-job"></a>Részklipkészítés feladat létrehozása

1. Válassza ki a letöltött Postman-gyűjteményben, **átalakítások és feladatok** -> **feladat létrehozása a Sub levágott**.
    
    A **PUT** kérelem néz ki:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Frissítse az átalakítási neve "transformName" környezeti változó értékét. 
1. Válassza ki a **törzs** lapra, és a "myOutputAsset" frissítse a kimeneti objektum nevét.

    ```
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
                "Ignite-short.mp4"
            ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Kattintson a **Küldés** gombra.

    Megjelenik a **válasz** lett létrehozva, valamint elküldött egy feladattal kapcsolatos információkat és a feladat állapotát. 

## <a name="next-steps"></a>További lépések

[Az egyéni átalakítási kódolása](custom-preset-rest-howto.md) 
