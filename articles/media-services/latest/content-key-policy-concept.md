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
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: a597ab3519f4ba1696e111622541bcab89488558
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/31/2019
ms.locfileid: "66425436"
---
# <a name="content-key-policies"></a>Tartalomkulcsszabályok

A Media Services élő és igény szerinti tartalmait az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek bármelyikét dinamikusan titkosított juttathat el: A Microsoft PlayReady, a Google Widevine és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek. 

A stream a titkosítási beállítások megadásához szeretne létrehozni egy [Streamelési házirend](streaming-policy-concept.md) társítsa a [Streamelési lokátor](streaming-locators-concept.md). Hoz létre a [tartalom kulcs házirend](https://docs.microsoft.com/rest/api/media/contentkeypolicies) konfigurálása hogyan a tartalomkulcs (biztonságos hozzáférést biztosít, amely a [eszközök](assets-concept.md)) végfelhasználók kézbesíti a rendszer. A tartalom kulcs házirend, amelyeknek teljesülniük kell ahhoz, hogy a kulcsokat a megadott konfiguráció az ügyfelek felé kell beállítani a követelményeknek (korlátozások). A tartalom kulcs házirend nem egyértelmű streamelési vagy letöltése szükséges. 

Általában, társítsa a **tartalom kulcs házirend** az a [Streamelési lokátor](streaming-locators-concept.md). Azt is megteheti, megadhatja a tartalom kulcs házirend belül egy [Streamelési házirend](streaming-policy-concept.md) (Ha egyéni Streamelési házirend létrehozása speciális forgatókönyvek). 

Javasoljuk, hogy a Media Services content kulcsok automatikus létrehozása. Általában akkor hosszú élettartamú van szükség, és ellenőrizze a szabályzatok megléte **első**. A kulcs lekéréséhez kell beolvasni a titkos kódok vagy hitelesítő adatokat, tekintse meg a következő példában egy külön művelet metódust hívja.

**Tartalom-kulcs házirendjei** frissíthetők. A kulcs kézbesítési gyorsítótárak frissítése, és vegye fel a frissített szabályzatot esetében akár 15 percet is igénybe vehet. 

> [!IMPORTANT]
> * Tulajdonságainak **Tartalomszabályzat kulcs** a DateTime típusú állandóan UTC formátumban vannak.
> * Házirendek korlátozott számú tervezzen a Media Services-fiókja és újra alkalmazza őket a Streamelési Lokátorok, amikor szükség van a ugyanazokkal a beállításokkal. További információkért lásd: [kvóták és korlátozások](limits-quotas-constraints.md).

### <a name="example"></a>Példa

A kulcs lekéréséhez használja **GetPolicyPropertiesWithSecretsAsync**, ahogyan az a [aláíró kulcs lekérése a meglévő házirend](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) példa.

## <a name="filtering-ordering-paging"></a>Szűrési, rendezési, stránkování

Lásd: [szűrése, rendezése, a Media Services entitások lapozás](entities-overview.md).

## <a name="next-steps"></a>További lépések

* [AES-128, a dinamikus titkosítás és a kulcstovábbítást használata](protect-with-aes128.md)
* [DRM a dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
