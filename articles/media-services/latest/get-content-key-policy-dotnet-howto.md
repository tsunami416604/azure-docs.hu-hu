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
ms.date: 04/09/2019
ms.author: juliako
ms.openlocfilehash: 49cc2b8c151053377f8f1da0792f10a06695b332
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496318"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>A meglévő házirend aláíró kulcs lekérése

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. V3 API-k nem adnak vissza titkos kulcsok és hitelesítő adatok a **első** vagy **lista** műveleteket. A kulcsok mindig null értékűek, üresek vagy törölve vannak a válaszból. A felhasználónak kell beolvasni a titkos kulcsok és hitelesítő adatok külön műveletet metódusának meghívása. A **olvasó** szerepkör nelze volat operations, műveletek, mint a Asset.ListContainerSas, StreamingLocator.ListContentKeys, ContentKeyPolicies.GetPolicyPropertiesWithSecrets, nelze volat. Önálló műveletek kellene lehetővé teszi a részletesebb RBAC biztonsági engedélyeinek beállítása egy egyéni szerepkör, ha szükséges.

További információkért lásd: [RBAC és a Media Services-fiókok](rbac-overview.md)

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

[Egy DRM-mel a content protection rendszert a hozzáférés-vezérléssel](design-multi-drm-system-with-access-control.md) 
