---
title: Aláírási kulcs beszerzése egy házirendből Azure Media Services v3 .NET használatával
description: Ez a témakör bemutatja, hogyan szerezhet be egy aláíró kulcsot a meglévő házirendből Media Services v3 .NET SDK használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: ff27ae0fd639316f03fe89ffc906561b3ef85f6f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251452"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Aláíró kulcs beszerzése a meglévő szabályzatból

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. a V3 API-k nem adnak vissza titkokat vagy hitelesítő adatokat a **Get** vagy a **List** műveletekhez. Tekintse meg a részletes magyarázatot. További információ: [RBAC és Media Services fiókok](rbac-overview.md)

A cikkben szereplő példa azt mutatja be, hogyan lehet a .NET használatával beolvasni az aláíró kulcsot a meglévő szabályzatból. 
 
## <a name="download"></a>Letöltés 

A következő parancs használatával klónozott egy GitHub-tárházat, amely a teljes .NET-mintát tartalmazza a gépen:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
A Secrets ContentKeyPolicy példa a [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) mappában található.

## <a name="get-contentkeypolicy-with-secrets"></a>ContentKeyPolicy beolvasása a titkokkal 

A kulcs eléréséhez használja a **GetPolicyPropertiesWithSecretsAsync**az alábbi példában látható módon.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>További lépések

[Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md) 
