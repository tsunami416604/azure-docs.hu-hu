---
title: Microsoft identity platform hitelesítési protokolljai | Microsoft dokumentumok
description: A Microsoft identity platform által támogatott hitelesítési protokollok áttekintése
author: rwike77
services: active-directory
manager: CelesteDG
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 43168ec7217d8f016857ba6dc54ca30bce2dd594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699291"
---
# <a name="microsoft-identity-platform-authentication-protocols"></a>Microsoft identity platform hitelesítési protokolljai

A Microsoft identity platform a legszélesebb körben használt hitelesítési és engedélyezési protokollok közül többet is támogat. Az ebben a szakaszban található témakörök ismertetik a támogatott protokollokat és azok megvalósítását a Microsoft identity platformon. A témakörök között szerepelt a támogatott jogcímtípusok áttekintése, az összevonási metaadatok használatának bemutatása, részletes OAuth 2.0. és saml 2.0 protokoll referencia dokumentációját, valamint egy hibaelhárítási szakaszt.

## <a name="authentication-protocols-articles-and-reference"></a>Hitelesítési protokollok cikkei és hivatkozásai

* [Fontos tudnivalók a kulcsváltás aláírásáról a Microsoft identitásplatformon](active-directory-signing-key-rollover.md) – Ismerje meg a Microsoft identity platform aláírókulcs-váltási ütemét, a kulcs automatikus frissítéséhez szükséges módosításokat, valamint a leggyakoribb alkalmazási forgatókönyvek frissítésének módjáról szóló vitát.
* [Támogatott jogkivonat- és jogcímtípusok](id-tokens.md) – Ismerje meg a microsofti identitásplatform által kioldott jogkivonatokat.
* [OAuth 2.0 a Microsoft identity platformon](v2-oauth2-auth-code-flow.md) – Ismerje meg az OAuth 2.0 megvalósítását a Microsoft identitásplatformján.
* [OpenID Connect 1.0](v2-protocols-oidc.md) – Ismerje meg, hogyan használhatja az OAuth 2.0-s hitelesítési protokollt.
* [Szolgáltatáshívások szolgáltatáshoz ügyfélhitelesítő adatokkal](v2-oauth2-client-creds-grant-flow.md) – Ismerje meg, hogyan használhatja az OAuth 2.0 ügyfélhitelesítő adatokat a szolgáltatás-szolgáltatáshívásokhoz.
* [Szolgáltatáshívások szolgáltatáshoz szolgáltatáshívások](v2-oauth2-on-behalf-of-flow.md) a folyamat nevében – Ismerje meg, hogyan használhatja az OAuth 2.0 A szolgáltatáshívások hoz a szolgáltatáshívásokat.
* [SAML protokollhivatkozás](active-directory-saml-protocol-reference.md) – Ismerje meg a Microsoft identity platform egyszeri bejelentkezési és egyszeri kijelentkezési SAML-profiljait.

## <a name="see-also"></a>Lásd még

* [A Microsoft identitásplatform – áttekintés](v2-overview.md)
* [Active Directory-kódminták](sample-v2-code.md)
