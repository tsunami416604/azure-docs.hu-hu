---
title: Azure Media Services v3 használatával egyéni átalakító kódolása |} Microsoft Docs
description: Ez a témakör bemutatja az Azure Media Services v3 használata egyéni átalakító kódolására.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/17/2018
ms.author: juliako
ms.openlocfilehash: d298070877a366d04b2df1ef8ac63b08f8771de0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655196"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Az egyéni átalakítási kódolással

Ha az Azure Media Services kódolási funkciójához kezdheti gyorsan, ahogyan az ágazatban kialakult bevált gyakorlaton alapuló ajánlott a beépített beállítások egyikét a [adatfolyam-fájlok](stream-files-tutorial-with-api.md) oktatóanyag, vagy választhatja azt hozhat létre egyéni előre definiált, amelyekre az adott forgatókönyv vagy az eszköz igények szerint. 

> [!Note]
> Az Azure Media Services v3 minden kódolási átviteli sebességet van bit / másodperc. Ez eltér a többi v2 Media Encoder Standard készleteket. Például a v2 sávszélességű 128 volna meg, de a v3 128000 lenne.

## <a name="download-the-sample"></a>A minta letöltése

Klónozza a GitHub-adattár, amely tartalmazza a teljes .NET Core minta a számítógépen a következő parancsot:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Az egyéni előre definiált minta található a [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) mappa.

## <a name="create-a-transform-with-a-custom-preset"></a>Hozzon létre egy egyéni készletet átalakítás 

Amikor hoz létre egy új [átalakítási](https://docs.microsoft.com/rest/api/media/transforms), meg kell adnia a kívánt műveleteket kimenetként létrehozásához. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. A következő **TransformOutput** egyéni kodekeket és a réteg kimeneti beállításokat hoz létre.

[Átalakítások](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható.  A Media Services v3 **beolvasása** módszerek az entitásokra **null** Ha az entitás nem létezik (a név nem betűérzékeny ellenőrzése).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>További lépések

[Adatfolyam-fájlok](stream-files-tutorial-with-api.md) 
