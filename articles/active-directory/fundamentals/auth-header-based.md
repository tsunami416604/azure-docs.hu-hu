---
title: Fejléc-alapú hitelesítés Azure Active Directory
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
ms.openlocfilehash: 4f364e4e14dd1b7c60cb81f06051d9dedd94396a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114257"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Fejléc-alapú hitelesítés Azure Active Directory

Az örökölt alkalmazások általában a fejléc-alapú hitelesítést használják. Ebben a forgatókönyvben egy felhasználó (vagy egy üzenet kezdeményezője) hitelesítve van egy közvetítő identitás megoldásban. A közbenső megoldás hitelesíti a felhasználót, és propagálja a szükséges Hypertext Transfer Protocol (HTTP) fejléceket a cél webszolgáltatásnak. A Azure Active Directory (AD) támogatja ezt a mintát az alkalmazásproxy szolgáltatásán keresztül, és integrálja más hálózati vezérlő megoldásokkal. 

A megoldásban az alkalmazásproxy távelérést biztosít az alkalmazáshoz, hitelesíti a felhasználót, és átadja az alkalmazás által igényelt fejléceket. 

## <a name="use-when"></a>A következő esetekben használja

A távoli felhasználóknak biztonságos egyszeri bejelentkezésre (SSO) van szükségük az olyan helyszíni alkalmazásokhoz, amelyek fejléc-alapú hitelesítést igényelnek.

![Építészeti rendszerkép fejléc-alapú hitelesítése](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>A System összetevői

* **Felhasználó**: az alkalmazásproxy által kiszolgált örökölt alkalmazásokhoz fér hozzá.

* **Webböngésző**: az alkalmazás külső URL-címének eléréséhez a felhasználó által kommunikáló összetevő.

* **Azure ad**: hitelesíti a felhasználót. 

* **Alkalmazásproxy szolgáltatás**: fordított proxyként viselkedik, hogy a felhasználótól érkező kérést küldjön a helyszíni alkalmazásnak. Az Azure AD-ben található, és a feltételes hozzáférési szabályzatok is érvénybe léphetnek.

* **Alkalmazásproxy-összekötő**: a Windows-kiszolgálókon telepített helyszíni környezettel biztosítható az alkalmazásokhoz való kapcsolódás. Csak kimenő kapcsolatokat használ. Az Azure AD-re adott válasz visszaadása.

* **Örökölt alkalmazások**: az alkalmazásproxy felhasználói kérelmeit fogadó alkalmazások. Az örökölt alkalmazás fogadja a szükséges HTTP-fejléceket a munkamenet beállításához és a válasz visszaadásához. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Fejléc-alapú hitelesítés implementálása az Azure AD-vel

* [Helyszíni alkalmazás hozzáadása távoli eléréshez az Application proxyn keresztül az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)  

* [Fejlécalapú hitelesítés alkalmazásproxyval és PingAccess segítségével történő egyszeri bejelentkezéshez](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-ping-access) 

* [Örökölt alkalmazások védelme alkalmazásszolgáltatási vezérlők és hálózatok segítségével](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
