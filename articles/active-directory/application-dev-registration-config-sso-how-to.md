---
title: Egy új több-bérlős alkalmazás konfigurálása |} Microsoft Docs
description: Hogyan konfigurálhatja az egyszeri bejelentkezés fejlesztés és regisztrálása az Azure ad-val egyéni alkalmazások.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: c9654a9d03769bbf0e9d0e2c477ad26cefc05150
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26612915"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>Egy új több-bérlős alkalmazás konfigurálása

Az alkalmazás összevont egyszeri bejelentkezést (SSO) engedélyezése automatikusan engedélyezi az Azure AD keresztül OpenID Connect, az SAML 2.0-s vagy a WS-Fed összevonását. Ha a végfelhasználók számára annak ellenére, hogy már rendelkezik az Azure AD egy meglévő munkamenetben bejelentkezni, valószínű, az alkalmazás nincs megfelelően konfigurálva.

* Ha az adal-t/MSAL használata esetén ellenőrizze, hogy **PromptBehavior** beállítása **automatikus** helyett **mindig**.

* Ha a mobilalkalmazások most felépítése, szükség lehet további konfigurációs közvetített vagy a nem közvetítőalapú egyszeri bejelentkezés engedélyezése.

Lásd: Android, [engedélyezése kereszt-alkalmazás SSO Android](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android).<br>

Az iOS számára, lásd: [Cross-SSO alkalmazás engedélyezése IOS](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios).

## <a name="next-steps"></a>Következő lépések

[Az Azure AD-egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Közötti App SSO Android engedélyezése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[Cross-SSO App engedélyezése iOS-ban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[AzureAD alkalmazások integrálása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Hozzájárulás és Permissioning AzureAD v2.0 az összevont alkalmazások](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[AzureAD StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
