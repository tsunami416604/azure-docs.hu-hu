---
title: Médiaprocesszor-példány beszerezni a REST | Microsoft dokumentumok
description: Megtudhatja, hogyan hozhat létre médiaprocesszor-összetevőt az Azure Media Services médiatartalmának kódolásához, konvertálásához, titkosításához vagy visszafejtéséhez.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 3d7b3922c9bb7eb79cd6436ba1b265714678fcc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774912"
---
# <a name="how-to-get-a-media-processor-instance"></a>Médiaprocesszor-példány beszerezése
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [Többi](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Áttekintés
A médiaprocesszorok olyan összetevők, amelyek egy adott video- vagy hangfeldolgozási feladatot kezelnek, például kódolást, formátumátalakítást, titkosítást vagy visszafejtést. A Media Services szolgáltatásba elküldött összes feladathoz médiaprocesszorra van szükség a video- vagy hangtartalom kódolásához, titkosításához vagy konvertálásához. 

## <a name="azure-media-processors"></a>Azure médiaprocesszorok 

Az alábbi témakör a médiaprocesszorok listáját tartalmazza:

* [Médiafeldolgozók kódolása](scenarios-and-availability.md#encoding-media-processors)
* [Elemzési médiafeldolgozók](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>Amikor a Media Services entitásait éri el, meghatározott fejlécmezőket és értékeket kell beállítania a HTTP-kérelmekben. További információt a [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md)című témakörben talál.

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz való csatlakozásról az [Access the Azure Media Services API azure AD-hitelesítéssel című témakörben](media-services-use-aad-auth-to-access-ams-api.md)talál további információt. 


## <a name="get-a-media-processor"></a>Médiaprocesszor beszereznie

A következő REST-hívás bemutatja, hogyan szerezhet be egy médiaprocesszor-példányt név szerint (ebben az esetben **a Media Encoder Standard).** 

Kérés:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.19
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


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések
Most, hogy már tudja, hogyan szerezhet be egy médiaprocesszor-példányt, nyissa meg a [Hogyan kódoljon egy eszköz](media-services-rest-get-started.md) cikket, amely bemutatja, hogyan használhatja a Media Encoder Standard egy eszköz kódolásához.

