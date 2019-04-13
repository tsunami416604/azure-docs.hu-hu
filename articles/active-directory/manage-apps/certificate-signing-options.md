---
title: Speciális tanúsítvány-aláírási beállítások előre integrált alkalmazások az Azure Active Directory a SAML-jogkivonat |} A Microsoft Docs
description: Ismerje meg, speciális tanúsítvány-aláírási beállítások előre integrált alkalmazások az Azure Active Directory a SAML-jogkivonat használatával
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bede53ef2bc05750be21f831fc0cb790a001c6c5
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549337"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Speciális tanúsítvány-aláírási beállítások katalógusbeli alkalmazásokat az Azure Active Directory a SAML-jogkivonat

Még ma az Azure Active Directory (Azure AD) több ezer előre integrált alkalmazás támogatja az Azure Active Directory-alkalmazás gyűjtemény. Az alkalmazások több mint 500 támogatja az egyszeri bejelentkezés használatával a [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0-s protokoll, mint például a [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) alkalmazás. Egy ügyfél egy alkalmazást az Azure AD-n keresztül SAML használatával hitelesíti magát, amikor az Azure AD egy tokent az alkalmazást (egy HTTP POST) küld. Az alkalmazás ellenőrzi, és használja a jogkivonatot az ügyfél nem kér a felhasználónévvel és jelszóval bejelentkezni. Ezek a SAML-jogkivonatok van bejelentkezve az Azure AD-ben és a standard algoritmusok által létrehozott egyedi tanúsítvánnyal.

Azure ad-ben használja az alapértelmezett beállítások némelyike a katalógus alkalmazásokat. Az alapértelmezett értékek beállítása az alkalmazások követelményeinek megfelelően.

Az Azure AD-ben állíthat be a tanúsítvány-aláírási beállítások és a tanúsítvány-aláírási algoritmus.

## <a name="certificate-signing-options"></a>Tanúsítvány-aláírási lehetőségek

Az Azure AD által támogatott három tanúsítvány-aláírási beállítások:

* **SAML-előfeltétel aláírása**. Ez az alapértelmezett beállítás a katalógus alkalmazások többsége van beállítva. Ha ezt a lehetőséget választja, az Azure AD Identitásszolgáltatóként (IdP), aláírja a SAML helyességi feltétel és a tanúsítvány a [X.509](https://wikipedia.org/wiki/X.509) tanúsítvány az alkalmazás.

* **SAML-válasz aláírása**. Ezt a beállítást, ha az Azure AD-Identitásszolgáltatóként aláírja a SAML-válasz az alkalmazás X.509 tanúsítvánnyal.

* **SAML-válasz és -előfeltétel aláírása**. Ezt a beállítást, ha az Azure AD-Identitásszolgáltatóként aláírja az X.509-tanúsítvány az alkalmazás teljes SAML-jogkivonatban.

## <a name="certificate-signing-algorithms"></a>A tanúsítvány-aláírási algoritmus

Az Azure AD két aláíró algoritmust, vagy a biztonságos kivonatoló algoritmus (SHA), a SAML-válasz aláírásához támogatja:

* **SHA-256**. Azure ad-ben az SAML-válasz aláírásához használja az alapértelmezett algoritmus. A legújabb algoritmus és biztonságosabb, mint az SHA-1. Az alkalmazások többsége támogatja az SHA-256 algoritmust. Ha egy alkalmazás támogatja a csak az SHA-1, az aláírási algoritmus, módosíthatja. Ellenkező esetben javasoljuk, hogy az SHA-256 algoritmust a SAML-válasz aláírása használjon.

* **AZ SHA-1**. Ez az algoritmus régebbi, és akkor számít kisebb biztonságos, mint az SHA-256 algoritmust. Ha egy alkalmazás támogatja az aláíró algoritmus, ezt a lehetőséget is válassza ki a **aláírási algoritmus** legördülő listából. Ezután az Azure AD SAML-válasz az SHA-1 algoritmussal jelentkezik.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Tanúsítvány-aláírási beállítások és az aláírási algoritmus módosítása

A tanúsítvány-aláírási algoritmus és a egy alkalmazás SAML-tanúsítvány aláírási beállítások módosításához válassza ki a kérdéses alkalmazás:

1. Az a [Azure Active Directory portálon](https://aad.portal.azure.com/), jelentkezzen be a fiókjába. A **Azure Active Directory felügyeleti központ** lap jelenik meg.
1. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet. A fiók a vállalati alkalmazások listája jelenik meg.
1. Válasszon ki egy alkalmazást. Az alkalmazás áttekintő oldala jelenik meg.

   ![Alkalmazások – Áttekintés lap](./media/certificate-signing-options/application-overview-page.png)

Ezután módosítsa a tanúsítvány-aláírási beállítások az adott alkalmazáshoz SAML-jogkivonat:

1. Az alkalmazás áttekintés oldal bal oldali panelen válassza ki a **egyszeri bejelentkezési**.

2. Ha a **állítsa be egyszeri bejelentkezést az SAML - előzetes verzió** lap jelenik meg, folytassa az 5. lépés.

3. Ha a **egyszeri bejelentkezési módszer** lap nem jelenik meg, jelölje be **egyszeri bejelentkezési mód módosítása** adott oldalának megnyitásához.

4. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML** ha rendelkezésre áll. (Ha **SAML** nem érhető el, az alkalmazás nem támogatja az SAML, és hagyja ki az eljárás, és a cikk többi.)

5. Az a **állítsa be egyszeri bejelentkezést az SAML - előzetes verzió** lapon, keresse meg a **SAML-aláíró tanúsítvány** címsor, és válassza ki a **szerkesztése** ikon (a Ceruza). A **SAML-aláíró tanúsítvány** lap jelenik meg.

   ![SAML-aláíró lap](./media/certificate-signing-options/saml-signing-page.png)

6. Az a **aláírási beállítás** legördülő menüben válassza ki **bejelentkezési SAML-válasz**, **bejelentkezési SAML helyességi feltétel**, vagy **bejelentkezési SAML-válasz és -előfeltétel**. Ezek a beállítások leírását a jelen cikk korábbi jelennek meg a [tanúsítvány-aláírási beállítások](#certificate-signing-options).

7. Az a **aláírási algoritmus** legördülő menüben válassza ki **SHA-1** vagy **SHA-256**. Ezek a beállítások leírását a jelen cikk korábbi jelennek meg a [tanúsítvány-aláírási algoritmusokat](#certificate-signing-algorithms) szakaszban.

8. Ha már elégedett a kiválasztott beállításokat, jelölje be **mentése** a alkalmazni az új SAML-aláíró tanúsítvány beállításait. Ellenkező esetben válassza a **X** elveti a módosításokat.

## <a name="next-steps"></a>További lépések

* [Egyszeri bejelentkezés alkalmazásokhoz, amelyek nem szerepelnek az Azure Active Directory-alkalmazás gyűjtemény konfigurálása](configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML-alapú egyszeri bejelentkezés hibaelhárítása](../develop/howto-v1-debug-saml-sso-issues.md)
