---
title: Az Azure Active Directory hitelesítési protokolljai |} A Microsoft Docs
description: A támogatott által az Azure Active Directory (AD) hitelesítési protokollokat áttekintése
documentationcenter: dev-center-name
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 7a838ae2-c24c-4304-b6c0-e77fb888e6c0
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: 81626cac41b1733f49e7fe1254c6b2bf8f5281cb
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442282"
---
# <a name="azure-active-directory-authentication-protocols"></a>Az Azure Active Directory hitelesítési protokolljai
Az Azure Active Directory (Azure AD) közül a legszélesebb körben használt hitelesítési és engedélyezési protokollokat támogatja. Ez a szakasz témakörei ismertetik a támogatott protokollok és a végrehajtásuk az Azure ad-ben. A témakörök támogatott jogcímtípusok, az összevonási metaadatok használatát bemutató áttekintését tartalmazza, az OAuth 2.0 részletes. SAML 2.0 protokoll dokumentációja és a egy hibaelhárítási szakaszt.

## <a name="authentication-protocols-articles-and-reference"></a>Hitelesítési protokollok, cikkek és referencia
* [Fontos információk kapcsolatos aláíró kulcs váltása az Azure ad-ben](active-directory-signing-key-rollover.md) – további információ az Azure AD aláírási kulcsváltás kiadása ütemben történik, a módosítások automatikusan frissíti a kulcs és a vitát a következőhöz: a leggyakoribb alkalmazási frissítése.
* [Támogatott Token- és jogcímtípusok](active-directory-token-and-claims.md) – ismerje meg a jogkivonatok, amelyek az Azure AD kibocsát jogcímeket.
* [Összevonási metaadatok](active-directory-federation-metadata.md) – ismerje meg, hogyan keresse meg és értelmezni a metaadatok dokumentumok, amelyek az Azure AD-hoz létre.
* [Az Azure AD OAuth 2.0-s](active-directory-protocols-oauth-code.md) – ismerje meg az OAuth 2.0 megvalósítását az Azure ad-ben.
* [OpenID Connect 1.0](active-directory-protocols-openid-connect-code.md) – ismerje meg, hogyan lehet OAuth 2.0, egy engedélyezési protokollt használnak a hitelesítéshez.
* [Szolgáltatások közötti hívások ügyfél-hitelesítő adatok](active-directory-protocols-oauth-service-to-service.md) -szolgáltatások közötti hívások OAuth 2.0 ügyfél-hitelesítő adatok megadási folyamatában használata.
* [Szolgáltatások közötti hívások-alapú meghatalmazásos folyamat](active-directory-protocols-oauth-on-behalf-of.md) -szolgáltatások közötti hívások OAuth 2.0-alapú meghatalmazásos folyamat használata.
* [SAML-protokoll referenciája](active-directory-saml-protocol-reference.md) – ismerje meg az Azure AD egyszeri bejelentkezés és egyszeri kijelentkezéses SAML profilok.

## <a name="see-also"></a>Lásd még:
[Az Azure Active Directory fejlesztői útmutatója](azure-ad-developers-guide.md)

[Az Active Directory-Kódminták](active-directory-code-samples.md)
