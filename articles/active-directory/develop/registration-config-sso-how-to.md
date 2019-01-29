---
title: Egy új több-bérlős alkalmazás konfigurálása |} A Microsoft Docs
description: Hogyan konfigurálása egyszeri bejelentkezéshez egy egyéni alkalmazás fejlesztésekor és regisztrálása az Azure ad-ben.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: be85456c94fbc156827157705bc29169bbae9cdd
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096760"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>Egy új több-bérlős alkalmazás konfigurálása

Az alkalmazás összevont egyszeri bejelentkezés (SSO) engedélyezése automatikusan engedélyezi az Azure AD-n keresztül az OpenID Connect, SAML 2.0 vagy WS-Fed összevonásával. Ha a végfelhasználók számára, annak ellenére, hogy már rendelkezik egy meglévő munkamenetben az Azure AD-bejelentkezés, valószínű, az alkalmazás nincs megfelelően konfigurálva.

* Ha az MSAL/adal-t használ, ellenőrizze, hogy **PromptBehavior** beállítása **automatikus** helyett **mindig**.

* Ha a mobilalkalmazás készít, szükség lehet további konfigurálás felügyelt vagy nem felügyelt egyszeri bejelentkezés engedélyezése.

Android-, lásd: [Cross-SSO alkalmazás engedélyezése Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

IOS-re, lásd: [engedélyezése Adatbázisközi alkalmazás egyszeri bejelentkezés az IOS-es](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>További lépések

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Platformfüggetlen Android alkalmazások SSO engedélyezése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Adatbázisközi-SSO alkalmazás engedélyezése IOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[Az Azure ad-alkalmazások integrálását ismertető](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Jóváhagyás és az Azure ad v2.0 csatornákban összevont alkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure ad stackoverflow-n](https://stackoverflow.com/questions/tagged/azure-active-directory)
