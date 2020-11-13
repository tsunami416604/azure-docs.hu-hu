---
title: Jelszó-alapú hitelesítés Azure Active Directory
description: A jelszó-alapú hitelesítés Azure Active Directory használatával történő megvalósítására szolgáló építészeti útmutató.
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
ms.openlocfilehash: 74815976036abafc56f97e622351a1018fe30504
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576932"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Jelszó-alapú hitelesítés Azure Active Directory

A jelszó-alapú egyszeri Sign-On (SSO) az alkalmazás meglévő hitelesítési folyamatát használja. Ha engedélyezi a jelszó-alapú egyszeri bejelentkezést, Azure Active Directory (Azure AD) a címtárban gyűjti, titkosítja és biztonságosan tárolja a felhasználói hitelesítő adatokat. Az Azure AD megadja a felhasználónevet és a jelszót az alkalmazásnak, amikor a felhasználó megpróbál bejelentkezni.

Válassza a jelszó-alapú egyszeri bejelentkezés lehetőséget, ha egy alkalmazás hozzáférési tokenek és fejlécek helyett felhasználónévvel és jelszóval hitelesíti magát. A jelszó-alapú SSO minden olyan felhőalapú alkalmazást támogat, amely HTML-alapú bejelentkezési oldallal rendelkezik. 

## <a name="use-when"></a>A következő esetekben használja

Az előhitelesítéssel kell védelemmel ellátnia az egyszeri bejelentkezést, és a jelszó-tárolón keresztül kell megadnia a webes alkalmazásokat.

![építészeti diagram](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>A System összetevői

* **Felhasználó** : a létrehozott alapú alkalmazást a saját alkalmazások közül vagy a webhely közvetlen meglátogatásával érheti el. 

* **Webböngésző** : az alkalmazás külső URL-címének eléréséhez a felhasználó által kommunikáló összetevő. A felhasználó az MyApps-bővítményen keresztül fér hozzá az űrlapalapú alkalmazáshoz. 

* **MyApps-bővítmény** : azonosítja a konfigurált jelszó-alapú SSO-alkalmazást, és a bejelentkezési űrlapon továbbítja a hitelesítő adatokat. A MyApps bővítmény telepítve van a webböngészőben. 

* **Azure ad** : hitelesíti a felhasználót.

## <a name="implement-password-based-sso-with-azure-ad"></a>Jelszó-alapú egyszeri bejelentkezés megvalósítása az Azure AD-vel

* [Mi a jelszó-alapú egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) 

* [Jelszó alapú egyszeri bejelentkezés konfigurálása felhőalapú alkalmazásokhoz ](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications)

* [Jelszó-alapú SSO konfigurálása helyszíni alkalmazásokhoz alkalmazásproxy segítségével](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting)

 
