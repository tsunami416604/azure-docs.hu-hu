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
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 882f4650c0a3d558ee06c96658b779f9f0c76f76
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322502"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>A meglévő házirend aláíró kulcs lekérése

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. A v3 API-k nem adnak vissza titkos kulcsokat vagy hitelesítő adatokat a **lekérési** vagy **listázási** művelet során. A kulcsok mindig null értékűek, üresek vagy törölve vannak a válaszból. Egy különálló műveleti metódust kell meghívnia a titkos kulcsok vagy hitelesítő adatok lekéréséhez. A különálló műveletekkel különböző RBAC biztonsági engedélyeket állíthat be, ha esetleg valamely API mégis lekér/megjelenít titkos kulcsokat, míg más API-k nem. A hozzáférés RBAC használatával való kezeléséről további információt a [hozzáférés RBAC használatával való kezeléséről](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest) szóló szakaszban talál.

Erre a következők szolgálnak példaként: 

* a rendszer nem ad vissza ContentKey értékeket a StreamingLocator lekérésében, 
* a rendszer nem ad vissza korlátozó kulcsokat a ContentKeyPolicy lekérésében, 
* a rendszer nem adja vissza az URL lekérdezési sztring részét (az aláírás eltávolításához) a feladat HTTP bemeneti URL-jei esetében.

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
