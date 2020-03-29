---
title: Videoklip az Azure Media Services REST szolgáltatással történő kódoláshoz
description: Ez a témakör azt ismerteti, hogyan lehet egy videót részklipként használni, amikor az Azure Media Services szolgáltatással kódol rest
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
ms.openlocfilehash: c39aded55fe36cb130459a4f6f119f872b1adbc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514323"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Részklip videó a Media Services szolgáltatással való kódoláskor - REST

A videót levághatja vagy részcsípheti, ha [a feladatot használva](https://docs.microsoft.com/rest/api/media/jobs)kódolja. Ez a funkció minden [olyan átalakítással](https://docs.microsoft.com/rest/api/media/transforms) működik, amely a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) készletekkel vagy a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) készletekkel épül fel. 

A jelen témakörben szereplő REST-példa létrehoz egy feladatot, amely levágja a videót a kódolási feladat elküldésekor. 

## <a name="prerequisites"></a>Előfeltételek

A témakörben ismertetett lépések végrehajtásához a következőket kell elvégeznie:

- [Hozzon létre egy Azure Media Services-fiókot.](create-account-cli-how-to.md)
- [A Postman konfigurálása az Azure Media Services REST API-hívásaihoz.](media-rest-apis-with-postman.md)
    
    Győződjön meg arról, hogy kövesse az [Azure AD-token beszerezni című](media-rest-apis-with-postman.md#get-azure-ad-token)témakör utolsó lépését. 
- Hozzon létre egy átalakítás és egy kimeneti eszközök. Láthatja, hogyan hozhat létre egy átalakítás és egy kimeneti eszközök a [kódolni egy távoli fájlt URL-cím alapján, és patak a videó - REST](stream-files-tutorial-with-rest.md) oktatóanyag.
- Tekintse át a [Kódolási koncepció](encoding-concept.md) témakörét.

## <a name="create-a-subclipping-job"></a>Alclipping feladat létrehozása

1. A letöltött Postman gyűjteményben válassza az **Átalakítások és feladatok** -> **létrehozása A segédvágással**című feladat lehetőséget.
    
    A **PUT** kérelem így néz ki:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Frissítse az "transformName" környezeti változó értékét az átalakító nevével. 
1. Válassza ki a **Törzs** lapot, és frissítse a "myOutputAsset" a kimeneti eszköz nevét.

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

    Megjelenik a **Válasz** a létrehozott és elküldött feladatra vonatkozó információkkal, valamint a feladat állapotával. 

## <a name="next-steps"></a>További lépések

[Hogyan kódolj egyéni átalakítással](custom-preset-rest-howto.md) 
