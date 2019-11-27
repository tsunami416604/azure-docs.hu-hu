---
title: Hozzon létre & Közzététel egyszeri bejelentkezési dokumentációt az alkalmazáshoz
description: Útmutató független szoftvergyártók számára a Azure Active Directory integrálásához
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb223ec8ab7b5c053136c78d3b4ca30ad4da4e18
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232282"
---
# <a name="create-and-publish-single-sign-on-documentation-for-your-application"></a>Egyszeri bejelentkezési dokumentáció létrehozása és közzététele az alkalmazáshoz   

## <a name="documentation-on-your-site"></a>A webhelyen található dokumentáció

A könnyű bevezetést a nagyvállalati szoftverek döntéseinek jelentős része képezi. Az egyszerű, könnyen követhető dokumentumok egyértelművé teszi az ügyfelek bevezetésének útját, és csökkentik a támogatási költségeket. Több ezer szoftvergyártó dolgozik a Microsoftnál, hogy mi működik.

Javasoljuk, hogy a webhelyen legalább a következő elemeket tartalmazza a dokumentációban.

* Az egyszeri bejelentkezés funkciójának bemutatása

  * Támogatott protokollok

  * Verzió és SKU

  * Támogatott identitás-szolgáltatók listája dokumentációs hivatkozásokkal

* Licencelési információk az alkalmazáshoz

* Szerepköralapú hozzáférés-vezérlés az egyszeri bejelentkezés konfigurálásához

* SSO-konfigurációs lépések

  * Az SAML felhasználói felületi konfigurációs elemei a szolgáltatótól várt értékekkel

  * Az azonosító szolgáltatók számára átadandó szolgáltatói információk

* If OIDC/OAuth

  * Az üzleti indoklások megadásához szükséges engedélyek listája

* Tesztelési lépések a próbaüzem felhasználói számára

* Hibaelhárítási információk, például hibakódok és üzenetek

* Támogatási mechanizmusok ügyfelek számára

## <a name="documentation-on-the-microsoft-site"></a>Dokumentáció a Microsoft webhelyén

Ha az alkalmazást az Azure Active Directory alkalmazás-katalógussal sorolja fel, amely az Azure piactéren is közzéteszi az alkalmazást, a Microsoft dokumentációt készít a kölcsönös ügyfeleinknek a lépésenkénti folyamatról. [Itt](https://aka.ms/appstutorial)láthat egy példát. Ezt a dokumentációt a katalógusba való beküldésük alapján hozza létre, és egyszerűen frissítheti, ha az alkalmazást a GitHub-fiók használatával módosítja.

## <a name="next-steps"></a>További lépések

[Az alkalmazás listázása az Azure AD Application Galleryben](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)