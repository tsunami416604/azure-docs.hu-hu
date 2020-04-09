---
title: Microsoft identity platform hitelesítési protokolljai
description: A Microsoft identity platform által támogatott hitelesítési protokollok áttekintése
author: rwike77
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/18/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 41ea41b4d7c181dad9246653a68c329387ac5381
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884681"
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
