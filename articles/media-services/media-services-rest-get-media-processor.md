---
title: "Hogyan kérhet egy adathordozó használatával REST processzorpéldány |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy adathordozó feldolgozó összetevő kódolni, formátum konvertálni, titkosítása vagy visszafejtése médiatartalom Azure Media Services."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako
ms.openlocfilehash: 4e673a92a9740b96eac20cdf5673395bacca8b77
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="how-to-get-a-media-processor-instance"></a>Egy Media processzorpéldány beszerzése
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Áttekintés
Media processzorok olyan összetevő, amely egy adott videó vagy a hang-feldolgozási feladata, például a kódolása, titkosítása vagy visszafejtése közben médiatartalmak konverzióval, kezeli. A Media Services küldött összes feladat media processzort kódolása, titkosítása vagy a video- vagy a tartalom átalakításához szükséges. 

## <a name="azure-media-processors"></a>Az Azure media processzor 

A következő témakör sorolja fel az adathordozó processzorok:

* [Kódolási media processzor](scenarios-and-availability.md#encoding-media-processors)
* [Elemzés media processzor](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>A Media Services entitások elérésekor be kell meghatározott fejlécmezők és értékek a HTTP-kérelmekre. További információkért lásd: [a Media Services REST API fejlesztési telepítő](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz kapcsolódáshoz információkért lásd: [elérni az Azure Media Services API-t az Azure AD-alapú hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Egy media processzor beolvasása

A következő REST-hívást bemutatja, hogyan kérhet egy adathordozó processzor-példány nevét (ebben az esetben **Media Encoder Standard**). 

A kérelem:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.17
    Host: media.windows.net

Válasz:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
Most, hogy hogyan kérhet egy adathordozó processzorpéldány ismeri, navigáljon a [egy eszköz kódolással](media-services-rest-get-started.md) cikket, amely bemutatja, hogyan kell a Media Encoder Standard segítségével egy eszköz kódolása.

