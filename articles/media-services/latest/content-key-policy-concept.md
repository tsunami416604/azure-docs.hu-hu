---
title: Tartalom-kulcs házirendjei, a Media Services – Azure |} A Microsoft Docs
description: Ez a cikk annak magyarázatát, Mik azok a Tartalomszabályzat kulcsot, és hogyan használják az Azure Media Services biztosítja.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d9e86c45d535862e0c3d02b3f331bc40ebb7f6c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733045"
---
# <a name="content-key-policies"></a>Tartalomkulcsszabályok

A Media Services élő és igény szerinti tartalmait az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek bármelyikét dinamikusan titkosított juttathat el: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek.

A stream a titkosítási beállítások megadásához szeretne létrehozni a [tartalom kulcs házirend](https://docs.microsoft.com/rest/api/media/contentkeypolicies) társítsa a **Streamelési lokátor**. A **tartalom kulcs házirend** konfigurálja a tartalomkulcsot a rendszer hogyan továbbítja a Media Services kulcs kézbesítési összetevője keresztül végfelhasználók. A Media Services engedélyezheti a automatikus létrehozása a tartalomkulcsot. Általában lenne hosszú élettartamú van szükség, és a Get-szabályzatok megléte ellenőrzése. A kulcs lekéréséhez kell beolvasni a titkos kódok vagy hitelesítő adatokat, tekintse meg a következő példában egy külön művelet metódust hívja.

**Tartalom-kulcs házirendjei** frissíthetők. Például előfordulhat, hogy szeretné frissíteni a szabályzatot, ha kell tennie egy kulcsrotálás. Frissítheti az elsődleges ellenőrzőkulcs és a meglévő házirendben alternatív ellenőrzőkulcsok listáját. A kulcs kézbesítési gyorsítótárak frissítése, és vegye fel a frissített szabályzatot esetében akár 15 percet is igénybe vehet. 

> [!IMPORTANT]
> * Tulajdonságainak **Tartalomszabályzat kulcs** a DateTime típusú állandóan UTC formátumban vannak.
> * Házirendek korlátozott számú tervezzen a Media Services-fiókja és újra alkalmazza őket a Streamelési Lokátorok, amikor szükség van a ugyanazokkal a beállításokkal. 

## <a name="example"></a>Példa

A kulcs lekéréséhez használja **GetPolicyPropertiesWithSecretsAsync**, az alábbi példában látható módon.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, stránkování

Lásd: [szűrése, rendezése, a Media Services entitások lapozás](entities-overview.md).

## <a name="next-steps"></a>További lépések

* [AES-128, a dinamikus titkosítás és a kulcstovábbítást használata](protect-with-aes128.md)
* [A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
