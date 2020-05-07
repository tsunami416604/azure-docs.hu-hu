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
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 61c0202d12756201d45fe829078d84382e44b54e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584297"
---
# <a name="authentication-vs-authorization"></a>Hitelesítés és engedélyezés

Ez a cikk a hitelesítéssel és engedélyezéssel foglalkozik, és röviden ismerteti, hogyan használhatja a Microsoft Identity platformot a webes alkalmazások, webes API-k vagy a védett webes API-kat hívó alkalmazások felhasználóinak hitelesítésére és engedélyezésére. Ha úgy látja, hogy nem ismeri a kifejezést, próbálja ki a [szószedetet](developer-glossary.md) vagy a [Microsoft Identity platform videóit](identity-videos.md) , amelyek az alapfogalmakat fedik le.

## <a name="authentication"></a>Hitelesítés

A **hitelesítés** az a folyamat, amelynek a bebizonyítása Ön szerint van. A hitelesítést (angolul authentication) szokás az AuthN rövidítéssel is jelölni. A Microsoft Identity platform implementálja az [OpenID Connect](https://openid.net/connect/) protokollt a hitelesítés kezelésére.

## <a name="authorization"></a>Engedélyezés

Az **Engedélyezés** a hitelesített fél engedélyének megadására irányuló művelet. Itt adhatja meg, hogy milyen adatelérési lehetőségekkel férhet hozzá, és hogy mit tehet az adott adattal. Az engedélyezést (angolul authorization) szokás az AuthZ rövidítéssel is jelölni. A Microsoft Identity platform implementálja az [OAuth 2,0](https://oauth.net/2/) protokollt az engedélyezés kezelésére.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Hitelesítés és engedélyezés a Microsoft Identity platform használatával

Ahelyett, hogy olyan alkalmazásokat hozna létre, amelyek mindegyike megtartja a saját felhasználónevét és jelszavát, ami magas adminisztrációs terhet jelent, ha több alkalmazásban kell felhasználókat felvennie vagy eltávolítania, az alkalmazások delegálni tudják a felelősséget egy központi identitás-szolgáltatónak.

Azure Active Directory (Azure AD) egy központi identitás-szolgáltató a felhőben. A hitelesítés és az engedélyezés delegálása olyan forgatókönyveket tesz lehetővé, mint például a feltételes hozzáférési szabályzatok, amelyek megkövetelik, hogy a felhasználó egy adott helyen legyen, a többtényezős hitelesítés használata, valamint a felhasználó egyszeri bejelentkezésének engedélyezése, majd automatikusan bejelentkezzen az összes olyan webalkalmazásba, amely ugyanazzal a központi címtárral rendelkezik. Ez a funkció **egyszeri bejelentkezésre (SSO)** hivatkozik.

A Microsoft Identity platform leegyszerűsíti az alkalmazások fejlesztőinek hitelesítését és engedélyezését azáltal, hogy szolgáltatásként szolgáltatja a szolgáltatást, és támogatja az iparági szabványnak megfelelő protokollokat, például a OAuth 2,0 és az OpenID Connect használatát, valamint a különböző platformokhoz készült nyílt forráskódú kódtárakat, amelyek segítségével gyorsan elkezdheti a kódolást. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat hozzanak létre, amelyek az összes Microsoft-identitást bejelentkeznek, a [Microsoft Graph](https://developer.microsoft.com/graph/), más Microsoft API-k vagy a fejlesztők által készített API-k meghívására. További információ: [a Microsoft Identity platform fejlődése](about-microsoft-identity-platform.md).

## <a name="next-steps"></a>További lépések

A hitelesítési és engedélyezési alapismeretekkel kapcsolatos egyéb témakörökhöz:

* Tekintse meg a [biztonsági jogkivonatokat](security-tokens.md) , hogy megtudja, hogyan használhatók a hozzáférési tokenek, a frissítési tokenek és az azonosító tokenek a hitelesítés és az engedélyezés során.
* Tekintse meg az alkalmazás [modelljét](application-model.md) , amelyből megismerheti az alkalmazás regisztrálásának folyamatát, hogy integrálható legyen a Microsoft Identity platformmal.
* Az [alkalmazás bejelentkezési folyamata](app-sign-in-flow.md) című témakörben megismerheti a webes, asztali és mobil alkalmazások bejelentkezési folyamatát a Microsoft Identity platformon.

További információ a Microsoft Identity platform által megvalósított protokollokról:

* Az OpenID Connect és a OAuth 2,0 szabványokkal kapcsolatos további információkért lásd: [OAuth 2,0 és OpenID Connect protokollok a Microsoft Identity platformon](active-directory-v2-protocols.md) .
* További információ arról, hogyan támogatja a Microsoft Identity platform az egyszeri bejelentkezést az [egyszeri bejelentkezéses SAML protokollon](single-sign-on-saml-protocol.md) .
