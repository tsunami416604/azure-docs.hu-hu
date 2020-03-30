---
title: Az Azure AD-alkalmazások SAML-tokentanúsítvány-aláírási lehetőségeinek speciális beállításai
description: Megtudhatja, hogy miként használhatja a speciális tanúsítványaláírási beállításokat az SAML-jogkivonatban az Azure Active Directoryban előintegrált alkalmazásokhoz
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc911ff06208b1fd0af7651c8274a45c958bf0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159199"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Speciális tanúsítványaláírási beállítások az AZURE Active Directoryban lévő katalógusalkalmazások SAML-tokenjében

Ma az Azure Active Directory (Azure AD) több ezer előre integrált alkalmazást támogat az Azure Active Directory Alkalmazástárban. Több mint 500 alkalmazás támogatja az egyszeri bejelentkezést a [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0 protokoll használatával, például a [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) alkalmazás használatával. Amikor egy ügyfél saml használatával hitelesíti magát egy alkalmazáshoz az Azure AD-n keresztül, az Azure AD egy jogkivonatot küld az alkalmazásnak (HTTP POST-on keresztül). Az alkalmazás ezután érvényesíti, és a jogkivonatot használja az ügyfél bejelentkezéséhez a felhasználónév és jelszó kérése helyett. Ezek az SAML-jogkivonatok az Azure AD-ben létrehozott egyedi tanúsítvánnyal és meghatározott szabványos algoritmusokkal vannak aláírva.

Az Azure AD a galériaalkalmazások néhány alapértelmezett beállítását használja. Az alapértelmezett értékek az alkalmazás követelményei alapján vannak beállítva.

Az Azure AD-ben beállíthatja a tanúsítvány-aláírási beállításokat és a tanúsítvány-aláíró algoritmust.

## <a name="certificate-signing-options"></a>Tanúsítvány-aláírási lehetőségek

Az Azure AD három tanúsítványaláírási lehetőséget támogat:

* **Saml állítás aláírása**. Ez az alapértelmezett beállítás a legtöbb katalógusalkalmazásban be van állítva. Ha ezt a beállítást választja, az Azure AD identitásszolgáltatóként (IdP) aláírja az SAML-állítást és -tanúsítványt az alkalmazás [X.509-es](https://wikipedia.org/wiki/X.509) tanúsítványával.

* **Saml válasz aláírása**. Ha ezt a lehetőséget választja, az Azure AD idP-ként aláírja az SAML-választ az alkalmazás X.509-es tanúsítványával.

* **Saml válasz és állítás aláírása**. Ha ezt a lehetőséget választja, az Azure AD idP-ként aláírja a teljes SAML-jogkivonatot az alkalmazás X.509-es tanúsítványával.

## <a name="certificate-signing-algorithms"></a>Tanúsítványaláíró algoritmusok

Az Azure AD támogatja a két aláíró algoritmusok, vagy biztonságos kivonatoló algoritmusok (ShA), hogy aláírja az SAML választ:

* **SHA-256- os.** Az Azure AD ezt az alapértelmezett algoritmust használja az SAML-válasz aláírásához. Ez a legújabb algoritmus, és biztonságosabb, mint az SHA-1. A legtöbb alkalmazás támogatja az SHA-256 algoritmust. Ha egy alkalmazás csak az SHA-1-et támogatja aláíró algoritmusként, módosíthatja azt. Ellenkező esetben azt javasoljuk, hogy az SHA-256 algoritmust használja az SAML-válasz aláírásához.

* **SHA-1.** Ez az algoritmus régebbi, és kevésbé biztonságosnak tekinti, mint az SHA-256. Ha egy alkalmazás csak ezt az aláíró algoritmust támogatja, ezt a lehetőséget az **Aláíró algoritmus** legördülő listában választhatja. Az Azure AD majd aláírja az SAML választ az SHA-1 algoritmus.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Tanúsítványaláírási beállítások és aláírási algoritmus módosítása

Az alkalmazás SAML tanúsítványaláíró beállításainak és a tanúsítványaláíró algoritmusmódosításához jelölje ki a kérdéses alkalmazást:

1. Az [Azure Active Directory portálon](https://aad.portal.azure.com/)jelentkezzen be a fiókjába. Megjelenik **az Azure Active Directory felügyeleti központ** lapja.
1. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet. Megjelenik a fiókjában lévő vállalati alkalmazások listája.
1. Válasszon ki egy alkalmazást. Megjelenik az alkalmazás áttekintő lapja.

   ![Példa: Alkalmazás áttekintő lapja](./media/certificate-signing-options/application-overview-page.png)

Ezután módosítsa a tanúsítványaláírási beállításokat az adott alkalmazás SAML-jogkivonatában:

1. Az alkalmazás áttekintése lap bal oldali ablaktáblájában válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Ha megjelenik **az Egyszeri bejelentkezés beállítása SAML - Preview** lapon, folytassa az 5.
1. Ha nem jelenik **meg az Egyetlen bejelentkezési módszer kiválasztása** lap, válassza az Egyszeri **bejelentkezési módok módosítása** lehetőséget az oldal megjelenítéséhez.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML** lehetőséget, ha elérhető. (Ha **az SAML** nem érhető el, az alkalmazás nem támogatja az SAML-t, és figyelmen kívül hagyhatja az eljárás és a cikk többi részét.)
1. Az **Egyszeri bejelentkezés beállítása SAML - Preview** lapon keresse meg az **SAML aláíró tanúsítvány** fejlécét, és válassza a **Szerkesztés** ikont (egy ceruzát). Megjelenik **az SAML aláíró tanúsítvány** lap.

   ![Példa: SAML aláíró tanúsítvány lap](./media/certificate-signing-options/saml-signing-page.png)

1. Az **Aláírási lehetőség** legördülő listában válassza az **SamL-válasz aláírása**, **Az SAML-állítás aláírása**vagy az **SAML-válasz és -állítás aláírása**lehetőséget. Ezeknek a beállításoknak a leírása a [tanúsítványaláírási beállítások](#certificate-signing-options)korábbi részében jelenik meg.
1. Az **Aláíró algoritmus** legördülő listában válassza az **SHA-1** vagy az **SHA-256 lehetőséget.** Ezeknek a beállításoknak a leírása a [tanúsítványaláíró algoritmusok](#certificate-signing-algorithms) szakaszban a cikk korábbi részében jelenik meg.
1. Ha elégedett a választással, válassza a **Mentés** gombot az új SAML aláíró tanúsítvány beállításainak alkalmazásához. Ellenkező esetben válassza ki az **X-et** a módosítások elvetéséhez.

## <a name="next-steps"></a>További lépések

* [Egyszeri bejelentkezés konfigurálása olyan alkalmazásokba, amelyek nem szerepelnek az Azure Active Directory alkalmazástárban](configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML-alapú egyszeri bejelentkezés – problémamegoldás](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
