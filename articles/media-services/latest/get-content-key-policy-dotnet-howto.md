---
title: Aláírási kulcs beszereznie egy szabályzatból az Azure Media Services 3-as v3-as hálózatát használva
description: Ez a témakör bemutatja, hogyan szerezhet be aláíró kulcsot a meglévő házirendből a Media Services 3.NET SDK-val.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2dea262fadb61adc9e219b76f9ac048c11e650d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065957"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Aláírókulcs lekérése a meglévő szabályzatból

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. a v3 API-k nem adnak vissza titkos kulcsokat vagy hitelesítő adatokat a **Lehívási** vagy **listaműveletekben.** A részletes magyarázat itt található: További információkért lásd [az RBAC- és Media Services-fiókokat.](rbac-overview.md)

A cikkben látható példa bemutatja, hogyan lehet a .NET használatával beszerezni egy aláíró kulcsot a meglévő házirendből. 
 
## <a name="download"></a>Letöltés 

Klónozzon egy GitHub-tárházat, amely a teljes .NET mintát tartalmazza a gépre a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
A ContentKeyPolicy titkos példával például található a [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM) mappában.

## <a name="get-contentkeypolicy-with-secrets"></a>Get ContentKeyPolicy titkokkal 

A kulcs hoz, használja **GetPolicyPropertiesWithSecretsAsync**, amint az alábbi példában látható.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>További lépések

[Hozzáférés-vezérléssel ellátott Multi-DRM-rendszerek tervezése](design-multi-drm-system-with-access-control.md) 
