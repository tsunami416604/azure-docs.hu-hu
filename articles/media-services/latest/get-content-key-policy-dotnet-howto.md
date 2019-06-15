---
title: Aláíró kulcs lekérése a meglévő házirend Media Services v3 .NET SDK-t – Azure |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan aláíró kulcs lekérése a meglévő házirend a Media Services v3 .NET SDK használatával.
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
ms.openlocfilehash: 58b6f49f4bbbd93fefb9b616f92baf7ef30f7deb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322634"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>A meglévő házirend aláíró kulcs lekérése

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. V3 API-k nem adnak vissza titkos kulcsok és hitelesítő adatok a **első** vagy **lista** műveleteket. Tekintse meg a részletes ismertetése: További információkért lásd: [RBAC és a Media Services-fiókok](rbac-overview.md)

Ebben a cikkben a példa bemutatja, hogyan a meglévő házirend aláíró kulcs lekérése a .NET használatával. 
 
## <a name="download"></a>Letöltés 

Klónozza a GitHub-adattár, amely tartalmazza a teljes .NET-minta a gépre a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
A titkos kulcsok példát ContentKeyPolicy található a [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) mappát.

## <a name="get-contentkeypolicy-with-secrets"></a>Titkos kulcsok ContentKeyPolicy beolvasása 

A kulcs lekéréséhez használja **GetPolicyPropertiesWithSecretsAsync**, az alábbi példában látható módon.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>További lépések

[Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md) 
