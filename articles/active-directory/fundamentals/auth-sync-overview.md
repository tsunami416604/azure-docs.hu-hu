---
title: A hitelesítési és szinkronizálási protokoll Azure Active Directory áttekintése
description: A hitelesítési minta megvalósítására szolgáló építészeti útmutató
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d881dc3fe3e3caa1058cf97834735910b0de1d9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114275"
---
# <a name="azure-active-directory-integrations-with-legacy-authentication-and-synchronization-protocols"></a>Az örökölt hitelesítési és szinkronizációs protokollok Azure Active Directory integrációk

Microsoft Azure Active Directory (Azure AD) számos hitelesítési és szinkronizálási protokollal való integrációt tesz lehetővé. A hitelesítési integrációk lehetővé teszik, hogy az Azure AD-t és annak biztonsági és felügyeleti funkcióit az örökölt hitelesítési módszereket használó alkalmazások kis vagy semmilyen módosításával használja. A szinkronizálási integrációk lehetővé teszik a felhasználók és csoportok adatainak szinkronizálását az Azure AD-be, majd a felhasználó Azure AD-kezelési képességeit. Egyes szinkronizálási minták lehetővé teszik az automatizált üzembe helyezést is.

## <a name="authentication-patterns"></a>Hitelesítési minták

A következő táblázat a hitelesítési mintákat és azok képességeit mutatja be. Válassza ki a megtekinteni kívánt hitelesítési minták nevét.

* Részletes leírás

* Mikor lehet használni

* Építészeti diagram

* A rendszerösszetevők magyarázata

* Hivatkozások az integráció megvalósításához

 

| Hitelesítési minták| Hitelesítés| Engedélyezés| Multi-Factor Authentication| Feltételes hozzáférés |
| - |- | - | - | - |
| [Fejléc-alapú hitelesítés](auth-header-based.md)|![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [LDAP-hitelesítés](auth-ldap.md)| ![Pipa jel](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2,0 hitelesítés](auth-oauth2.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [OIDC-hitelesítés](auth-oidc.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [Jelszó alapú egyszeri bejelentkezéses hitelesítés](auth-password-based-sso.md )| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [RADIUS-hitelesítés]( auth-radius.md)| ![Pipa jel](./media/authentication-patterns/check.png)| | ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [Távoli asztali átjáró szolgáltatások](auth-remote-desktop-gateway.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [SAML-hitelesítés](auth-saml.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |
| [Windows-hitelesítés – Kerberos által korlátozott delegálás](auth-kcd.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>Szinkronizálási minták

A következő táblázat a szinkronizálási mintákat és azok képességeit mutatja be. Válassza ki a megtekinteni kívánt minta nevét

* Részletes leírás

* Mikor lehet használni

* Építészeti diagram

* A rendszerösszetevők magyarázata

* Hivatkozások az integráció megvalósításához



| Szinkronizálási minta| Címtár-szinkronizálás| Felhasználók átadása |
| - | - | - |
| [Címtár-szinkronizálás](sync-directory.md)| ![Pipa jel](./media/authentication-patterns/check.png)|  |
| [LDAP-szinkronizálás](sync-ldap.md)| ![Pipa jel](./media/authentication-patterns/check.png)|  |
| [SCIM szinkronizálása](sync-scim.md)| ![Pipa jel](./media/authentication-patterns/check.png)| ![Pipa jel](./media/authentication-patterns/check.png) |

