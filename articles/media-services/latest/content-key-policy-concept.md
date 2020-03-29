---
title: Tartalomkulcs-házirendek a Media Services szolgáltatásban – Azure | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogy mik a tartalomkulcs-szabályzatok, és hogyan használják azOkat az Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/26/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7ddef1e78b4f8f62145e10b4cabc4537e28aba2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74969904"
---
# <a name="content-key-policies"></a>Tartalomkulcsszabályok

A Media Services segítségével az Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (DRM) rendszer bármelyikével dinamikusan titkosított élő és igény szerinti tartalmakat is kézbesíthet: Microsoft PlayReady, Google Widevine és Apple FairPlay. A Media Services szolgáltatást is biztosít az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licencek hivatalos ügyfelek nek történő kézbesítéséhez. 

Az adatfolyam titkosítási beállításainak megadásához létre kell hoznia egy [streamelési szabályzatot,](streaming-policy-concept.md) és hozzá kell rendelnie azt a [streamelési lokátorhoz.](streaming-locators-concept.md) A [Tartalomkulcs-házirendet](https://docs.microsoft.com/rest/api/media/contentkeypolicies) annak konfigurálásához hozza létre, hogy a tartalomkulcs (amely biztonságos hozzáférést biztosít az [eszközökhöz)](assets-concept.md)kézbesítése a végfelhasználókhoz. Be kell állítania a tartalomkulcs-házirend követelményeit (korlátozásait), amelyeket teljesíteni kell ahhoz, hogy a megadott konfigurációval rendelkező kulcsokat kézbesítsék az ügyfeleknek. A tartalomkulcs-házirend nem szükséges a tiszta streameléshez vagy letöltéshez. 

Általában a tartalomkulcs-házirendet társítja a [streamelési lokátorhoz.](streaming-locators-concept.md) Azt is megteheti, hogy a tartalomkulcs-házirendet egy [streamelési szabályzaton](streaming-policy-concept.md) belül adja meg (ha egyéni streamelési szabályzatot hoz létre speciális forgatókönyvekhez). 

## <a name="best-practices-and-considerations"></a>Bevált gyakorlatok és szempontok

> [!IMPORTANT]
> Kérjük, tekintse át az alábbi ajánlásokat.

* A Media Service-fiókhoz korlátozott házirendeket kell terveznie, és újra fel kell használnia őket a streamelési lokátorokhoz, amikor ugyanazokra a beállításokra van szükség. További információt a [Kvóták és korlátozások](limits-quotas-constraints.md)című témakörben talál.
* A tartalomkulcs-házirendek elérhetők. A kulcskézbesítési gyorsítótárak frissítése és a frissített házirend frissítése akár 15 percet is igénybe vehet. 

   A házirend frissítésével felülírja a meglévő CDN-gyorsítótárat, ami lejátszási problémát okozhat a gyorsítótárazott tartalmat használó ügyfelek számára.  
* Azt javasoljuk, hogy ne hozzon létre új tartalomkulcs-szabályzatot az egyes eszközökhöz. Az azonos házirend-beállításokat igénylő eszközök közötti azonos tartalmi kulcsházirend ek közötti megosztásának fő előnyei a következők:
   
   * Kis számú házirend kezelése egyszerűbb.
   * Ha frissítéseket kell végrehajtania a tartalomkulcs-házirendhez, a módosítások szinte azonnal életbe lépnek az összes új licenckérelemben.
* Ha új szabályzatot kell létrehoznia, létre kell hoznia egy új streamelési lokátort az eszközhöz.
* Javasoljuk, hogy a Media Services automatikusan generálja a tartalomkulcsot. 

   Általában egy hosszú élettartamú kulcsot kell használnia, és a [Get](https://docs.microsoft.com/rest/api/media/contentkeypolicies/get)segítségével ellenőrizheti a tartalomkulcs-házirend létezését. A kulcs beszerezéséhez meg kell hívnia egy külön műveletmódszert a titkos kulcsok vagy hitelesítő adatok lehívásához, lásd a következő példát.

## <a name="example"></a>Példa

A kulcs hoz, `GetPolicyPropertiesWithSecretsAsync`használja a , ahogy az a [aláírása kulcs beszereznia a meglévő házirend példából.](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets)

## <a name="filtering-ordering-paging"></a>Szűrés, rendelés, lapozás

Lásd: [Media Services-entitások szűrése, rendelése, lapozása.](entities-overview.md)

## <a name="additional-notes"></a>További megjegyzések

* A `Datetime` tartalomkulcs-házirendek típusának tulajdonságai mindig UTC formátumúak.
* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="next-steps"></a>További lépések

* [Az AES-128 dinamikus titkosítás és a kulcskézbesítési szolgáltatás használata](protect-with-aes128.md)
* [A DRM dinamikus titkosítási és licenctovábbítási szolgáltatás használata](protect-with-drm.md)
* [KódolásHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
