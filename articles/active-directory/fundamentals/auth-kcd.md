---
title: Kerberos által korlátozott delegálás Azure Active Directory
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
ms.openlocfilehash: 77f90cd7aa8d972226a8f134eaa7b3abfe7bea66
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114260"
---
# <a name="windows-authentication---kerberos-constrained-delegation-with-azure-active-directory"></a>Windows-hitelesítés – Kerberos által korlátozott delegálás Azure Active Directory

A Kerberos által korlátozott delegálás (KCD) korlátozott delegálást biztosít az erőforrások között, és a szolgáltatás-elvi neveken alapul. Szükség van a tartományi rendszergazdákra a delegálások létrehozásához, és egyetlen tartományra korlátozódik. Az erőforrás-alapú KCD gyakran használják a Kerberos-hitelesítés biztosítására olyan webalkalmazásokhoz, amelyek egy Active Directory erdőben több tartományba tartozó felhasználókkal rendelkeznek.

A Azure Active Directory Application Proxy egyszeri bejelentkezést (SSO) és távoli hozzáférést biztosíthat olyan KCD-alapú alkalmazásokhoz, amelyek Kerberos-jegyet igényelnek a hozzáféréshez és a Kerberos által korlátozott delegáláshoz (KCD).

Lehetővé teszi az egyszeri bejelentkezést az integrált Windows-hitelesítést (IWA) használó helyszíni KCD-alkalmazásokhoz, ha az alkalmazásproxy-összekötők számára engedélyezi a felhasználók megszemélyesítését Active Directoryban. Az alkalmazásproxy-összekötő ezt az engedélyt használja a jogkivonatok küldésére és fogadására a felhasználók nevében.

## <a name="use-when"></a>A következő esetekben használja

Szükség van a távoli hozzáférés biztosítására, az előhitelesítéssel való védelemre, valamint a helyszíni IWA-alkalmazásokhoz való egyszeri bejelentkezésre.

![Architektúra ábrája](./media/authentication-patterns/kcd-auth.png)

## <a name="components-of-system"></a>A System összetevői

* **Felhasználó**: az alkalmazásproxy által kiszolgált örökölt alkalmazáshoz fér hozzá.

* **Webböngésző**: az alkalmazás külső URL-címének eléréséhez a felhasználó által kommunikáló összetevő.

* **Azure ad**: hitelesíti a felhasználót. 

* **Alkalmazásproxy szolgáltatás**: fordított proxyként viselkedik, hogy a felhasználótól érkező kérést küldjön a helyszíni alkalmazásnak. Az Azure AD-ban található. Az alkalmazásproxy is kényszerítheti a feltételes hozzáférési szabályzatok alkalmazását.

* **Alkalmazásproxy-összekötő**: a helyszíni Windows-kiszolgálókon telepítve van az alkalmazáshoz való kapcsolódás. Az Azure AD-re adott válasz visszaadása. KCD-egyeztetést végez Active Directory használatával, megszemélyesítve a felhasználót, hogy Kerberos-jogkivonatot kapjon az alkalmazáshoz.

* **Active Directory**: a Kerberos-tokent elküldi az alkalmazásnak az alkalmazásproxy-összekötőnek.

* **Örökölt alkalmazások**: az alkalmazásproxy felhasználói kérelmeit fogadó alkalmazások. Az örökölt alkalmazások visszaküldik a választ az alkalmazásproxy-összekötőnek.

## <a name="implement-windows-authentication-kcd-with-azure-ad"></a>Windows-hitelesítés (KCD) implementálása az Azure AD-vel

* [Kerberos általi korlátozott delegálás alkalmazásproxyval történő, egyszeri alkalmazásbejelentkezéshez](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd) 

* [Helyszíni alkalmazás hozzáadása a távoli eléréshez az alkalmazásproxy használatával Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
