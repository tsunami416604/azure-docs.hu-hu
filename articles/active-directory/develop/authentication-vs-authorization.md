---
title: Hitelesítés és engedélyezés | Azure
titleSuffix: Microsoft identity platform
description: Tudnivalók a hitelesítés és engedélyezés alapjairól a Microsoft Identity platformon (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: b905b8f3fe99b83bafdd61b1daa25549354c5275
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926782"
---
# <a name="authentication-vs-authorization"></a>Hitelesítés és engedélyezés

Ez a cikk a hitelesítéssel és engedélyezéssel foglalkozik, és röviden ismerteti, hogyan használhatja a Microsoft Identity platformot a webes alkalmazások, webes API-k vagy a védett webes API-kat hívó alkalmazások felhasználóinak hitelesítésére és engedélyezésére. Ha úgy látja, hogy nem ismeri a kifejezést, próbálja ki a [szószedetet](developer-glossary.md) vagy a [Microsoft Identity platform videóit](identity-videos.md) , amelyek az alapfogalmakat fedik le.

## <a name="authentication"></a>Hitelesítés

A **hitelesítés** az a folyamat, amelynek a bebizonyítása Ön szerint van. A hitelesítést (angolul authentication) szokás az AuthN rövidítéssel is jelölni. A Microsoft Identity platform implementálja az [OpenID Connect](https://openid.net/connect/) és az [SAML 2,0](http://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-tech-overview-2.0.html) protokollt a hitelesítés kezelésére.

## <a name="authorization"></a>Engedélyezés

Az **Engedélyezés** a hitelesített fél engedélyének megadására irányuló művelet. Itt adhatja meg, hogy milyen adatelérési lehetőségekkel férhet hozzá, és hogy mit tehet az adott adattal. Az engedélyezést (angolul authorization) szokás az AuthZ rövidítéssel is jelölni. A Microsoft Identity platform implementálja az [OAuth 2,0](https://oauth.net/2/) protokollt az engedélyezés kezelésére.

## <a name="authentication-and-authorization-using-microsoft-identity-platform"></a>Hitelesítés és engedélyezés a Microsoft Identity platform használatával

Ahelyett, hogy olyan alkalmazásokat hozna létre, amelyek mindegyike megtartja a saját felhasználónevét és jelszavát, ami magas adminisztrációs terhet jelent, ha több alkalmazásban kell felhasználókat felvennie vagy eltávolítania, az alkalmazások delegálni tudják a felelősséget egy központi identitás-szolgáltatónak.

Azure Active Directory (Azure AD) egy központi identitás-szolgáltató a felhőben. A hitelesítés és az engedélyezés delegálása olyan forgatókönyveket tesz lehetővé, mint például a feltételes hozzáférési szabályzatok, amelyek megkövetelik, hogy a felhasználó egy adott helyen legyen, a többtényezős hitelesítés használata, valamint a felhasználó egyszeri bejelentkezésének engedélyezése, majd automatikusan bejelentkezzen az összes olyan webalkalmazásba, amely ugyanazzal a központi címtárral rendelkezik. Ez a funkció **egyszeri bejelentkezésre (SSO)** hivatkozik.

A Microsoft Identity platform leegyszerűsíti az alkalmazások fejlesztőinek hitelesítését és engedélyezését azáltal, hogy szolgáltatásként szolgáltatja az identitást, és támogatja az iparági szabványnak megfelelő protokollokat, például a OAuth 2,0, az OpenID Connect és az SAML 2,0, valamint a különböző platformokhoz készült nyílt forráskódú kódtárakat, amelyek segítségével gyorsan elindíthatja a kódolást. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat hozzanak létre, amelyek az összes Microsoft-identitást bejelentkeznek, a [Microsoft Graph](https://developer.microsoft.com/graph/), más Microsoft API-k vagy a fejlesztők által készített API-k meghívására. További információ: [a Microsoft Identity platform fejlődése](about-microsoft-identity-platform.md).

A következő a Microsoft Identity platform által használt különböző protokollok rövid összevetése:

* **OAuth vs. OpenID Connect**: a rendszer a OAuth használja az engedélyezéshez és az OpenID Connect (OIDC) hitelesítéshez. Az OpenID Connect a OAuth 2,0-re épül, így a terminológia és a folyamat a kettő között hasonló. Akár egyszerre is hitelesítheti a felhasználót (az OpenID Connect használatával), és engedélyt kaphat egy olyan védett erőforrás elérésére, amelyet a felhasználó birtokol (az OAuth 2,0 használatával) egy kérelemben. További információ: [OAuth 2,0 és OpenID Connect protokollok](active-directory-v2-protocols.md) és [OpenID Connect Protocol](v2-protocols-oidc.md).
* **OAuth és SAML**: az OAuth az engedélyezéshez, az SAML pedig a hitelesítéshez használatos. Tekintse meg a [Microsoft Identity platform és a OAuth 2,0 SAML-tulajdonos állítási folyamatát](v2-saml-bearer-assertion.md) , hogy a két protokoll hogyan használható egyszerre a felhasználó hitelesítésére (SAML használatával) és a védett erőforrásokhoz való hozzáférés engedélyezésére (a OAuth 2,0 használatával).
* **OpenID Connect és SAML**: az OpenID Connect és az SAML is a felhasználók hitelesítésére szolgál, és az egyszeri bejelentkezés engedélyezésére szolgál. Az SAML-hitelesítést gyakran használják olyan identitás-szolgáltatókkal, mint az Azure AD-ba összevont Active Directory összevonási szolgáltatások (AD FS) (ADFS), ezért gyakran használják a vállalati alkalmazásokban.

## <a name="next-steps"></a>További lépések

A hitelesítési és engedélyezési alapismeretekkel kapcsolatos egyéb témakörökhöz:

* Tekintse meg a [biztonsági jogkivonatokat](security-tokens.md) , hogy megtudja, hogyan használhatók a hozzáférési tokenek, a frissítési tokenek és az azonosító tokenek a hitelesítés és az engedélyezés során.
* Tekintse meg az alkalmazás [modelljét](application-model.md) , amelyből megismerheti az alkalmazás regisztrálásának folyamatát, hogy integrálható legyen a Microsoft Identity platformmal.
* Az [alkalmazás bejelentkezési folyamata](app-sign-in-flow.md) című témakörben megismerheti a webes, asztali és mobil alkalmazások bejelentkezési folyamatát a Microsoft Identity platformon.

További információ a Microsoft Identity platform által megvalósított protokollokról:

* Az OpenID Connect és a OAuth 2,0 szabványokkal kapcsolatos további információkért lásd: [OAuth 2,0 és OpenID Connect protokollok a Microsoft Identity platformon](active-directory-v2-protocols.md) .
* További információ arról, hogyan támogatja a Microsoft Identity platform az egyszeri bejelentkezést az [egyszeri bejelentkezéses SAML protokollon](single-sign-on-saml-protocol.md) .
