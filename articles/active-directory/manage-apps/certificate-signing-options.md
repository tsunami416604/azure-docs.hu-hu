---
title: Speciális tanúsítvány-aláírási beállítások előre integrált alkalmazások az Azure Active Directory a SAML-jogkivonat |} A Microsoft Docs
description: Ismerje meg, speciális tanúsítvány-aláírási beállítások előre integrált alkalmazások az Azure Active Directory a SAML-jogkivonat használatával
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/07/2017
ms.author: barbkess
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: d5921b9b74bfa7e32cc8dc2f9fdc5f93d4027abb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366501"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Speciális tanúsítvány-aláírási beállítások katalógusbeli alkalmazásokat az Azure Active Directory a SAML-jogkivonat
Még ma az Azure Active Directory (Azure AD) több ezer előre integrált alkalmazás támogatja az Azure Active Directory-alkalmazás gyűjtemény. Ez a szám, amely támogatja az egyszeri bejelentkezéshez a SAML 2.0 protokoll használatával több mint 500 alkalmazásokat tartalmaz. Amikor egy felhasználó egy alkalmazást az Azure AD-n keresztül SAML használatával hitelesíti magát, az Azure AD egy tokent az alkalmazást (egy HTTP POST) küld. Ezt követően az alkalmazás ellenőrzi, és jelentkezzen be a felhasználó nem egy felhasználónevet és jelszót kér a jogkivonat alapján. Ezek a SAML-jogkivonatok van bejelentkezve az Azure AD-ben és a standard algoritmusok által létrehozott egyedi tanúsítvánnyal.

Azure ad-ben használja az alapértelmezett beállítások némelyike a katalógus alkalmazásokat. Az alapértelmezett értékek beállítása az alkalmazások követelményeinek megfelelően.

Az Azure AD támogatja a speciális tanúsítvány-aláírási beállítások. Ezek a beállítások kiválasztásához először válassza ki a **speciális tanúsítvány-aláírási beállítások megjelenítése** jelölőnégyzetet:

![Speciális tanúsítvány-aláírási beállítások megjelenítése](./media/certificate-signing-options/saml-advance-certificate.png)

Miután kiválasztotta ezt a jelölőnégyzetet, akkor állíthatja a tanúsítvány-aláírási beállítások és a tanúsítvány-aláírási algoritmus.

## <a name="certificate-signing-options"></a>A tanúsítvány-aláírási beállítások

Az Azure AD által támogatott három tanúsítvány-aláírási beállítások:

* **SAML-előfeltétel aláírása**. Ez az alapértelmezett beállítás a katalógus alkalmazások többsége van beállítva. Ha ezt a lehetőséget választja, az Azure AD-Identitásszolgáltatóként aláírja a SAML helyességi feltétel és a tanúsítvány az X509 tanúsítvány az alkalmazás. Az aláíró algoritmus, amely a kijelölt használ, a **aláírási algoritmus** legördülő listából válassza ki.

* **SAML-válasz aláírása**. Ezt a beállítást, ha az Azure AD-Identitásszolgáltatóként az X509 SAML-válasz aláírja a tanúsítványt az alkalmazás. Az aláíró algoritmus, amely a kijelölt használ, a **aláírási algoritmus** legördülő listából válassza ki.

* **SAML-válasz és -előfeltétel aláírása**. Ha ezt a beállítást, az Azure AD-Identitásszolgáltatóként aláírja az X509 teljes SAML-jogkivonat tanúsítványa az alkalmazást. Az aláíró algoritmus, amely a kijelölt használ, a **aláírási algoritmus** legördülő listából válassza ki.

    ![A tanúsítvány-aláírási beállítások](./media/certificate-signing-options/saml-signing-options.png)

## <a name="certificate-signing-algorithms"></a>A tanúsítvány-aláírási algoritmus

Az Azure AD SAML-válasz aláírása két aláírási algoritmusokat támogatja:

* **SHA-256 ALGORITMUST**. Azure ad-ben az SAML-válasz aláírásához használja az alapértelmezett algoritmus. A legújabb algoritmust, és úgy viselkedik, mint, több biztonságos, mint az SHA-1. Az alkalmazások többsége támogatja az SHA-256 algoritmust. Ha egy alkalmazás támogatja a csak az SHA-1, az aláírási algoritmus, módosíthatja. Ellenkező esetben javasoljuk, hogy az SHA-256 algoritmust a SAML-válasz aláírása használjon.

    ![SHA-256 tanúsítvány-aláírási algoritmus](./media/certificate-signing-options/saml-signing-algo-sha256.png)

* **AZ SHA-1**. Ez a régebbi algoritmust, és akkor számít kisebb biztonságos SM-256-nál. Ha egy alkalmazás támogatja az aláíró algoritmus, ezt a lehetőséget is válassza ki a **aláírási algoritmus** legördülő listából. Ezután az Azure AD SAML-válasz az SHA-1 algoritmussal jelentkezik.

    ![Az SHA-1 tanúsítvány-aláírási algoritmus](./media/certificate-signing-options/saml-signing-algo-sha1.png)

## <a name="next-steps"></a>További lépések
* [Alkalmazások kezelése az Azure Active Directoryban vonatkozó cikkek jegyzéke](../active-directory-apps-index.md)
* [Egyszeri bejelentkezés alkalmazásokhoz, amelyek nem szerepelnek az Azure Active Directory-alkalmazás gyűjtemény konfigurálása](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [SAML-alapú egyszeri bejelentkezés hibaelhárítása](../develop/active-directory-saml-debugging.md)


