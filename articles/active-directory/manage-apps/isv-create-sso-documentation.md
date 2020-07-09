---
title: Hozzon létre & közzététel egyszeri bejelentkezési dokumentációt az alkalmazáshoz
description: Útmutató független szoftvergyártók számára a Azure Active Directory integrálásához
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: kenwith
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c758e90548dd22b5abfb731f674f83dfbde9819
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85955981"
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

## <a name="next-steps"></a>Következő lépések

[Az alkalmazás listázása az Azure AD Application Galleryben](https://docs.microsoft.com/Azure/active-directory/develop/howto-app-gallery-listing)
