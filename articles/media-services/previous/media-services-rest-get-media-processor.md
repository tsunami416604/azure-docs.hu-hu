---
title: A REST használatával Médiafeldolgozót példány beszerzése |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre kódolása, formátum konvertálása, titkosítása vagy visszafejtése a médiatartalmak az Azure Media Services media processzor összetevő.
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
ms.openlocfilehash: d342cff6d322195ee88a74215f814be7d702aa5e
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294127"
---
# <a name="how-to-get-a-media-processor-instance"></a>Egy Media processzorpéldány beszerzése
> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)
> 
> 

## <a name="overview"></a>Áttekintés
A Médiaelemzés Médiafeldolgozói egy összetevő, amely egy adott videó vagy hang feldolgozási feladat, például a kódolás, formátumátalakítás, titkosításakor vagy visszafejtésekor médiatartalmak kezeli. Minden feladat elküldve a Media Services egy médiaprocesszorral kódolása, titkosítása vagy a video- és audiotartalmak tartalom átalakításához szükséges. 

## <a name="azure-media-processors"></a>Az Azure médiaelemzés médiafeldolgozói 

A következő témakör ismerteti a médiaelemzés médiafeldolgozói listáját tartalmazza:

* [Médiafeldolgozók kódolása](scenarios-and-availability.md#encoding-media-processors)
* [Elemzési médiafeldolgozók](scenarios-and-availability.md#analytics-media-processors)

>[!NOTE]
>A Media Services entitások elérésekor a be kell állítani a HTTP-kérelmekre a meghatározott fejlécmezők és értékek. További információkért lásd: [beállítása a Media Services REST API-k fejlesztését](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-t kapcsolódás információkért lásd: [eléréséhez az Azure Media Services API Azure AD-hitelesítés](media-services-use-aad-auth-to-access-ams-api.md). 


## <a name="get-a-media-processor"></a>Get-médiafeldolgozót.

Az alábbi REST-hívás bemutatja, hogyan egy adathordozó processzorpéldány beszerezni a nevét (ebben az esetben **Media Encoder Standard**). 

Kérés:

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
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
Most, hogy hogyan tehet szert egy adathordozó processzorpéldány ismeri, nyissa meg a [adategység kódolása](media-services-rest-get-started.md) amely adategység kódolása a Media Encoder Standard használatát bemutató cikket.

