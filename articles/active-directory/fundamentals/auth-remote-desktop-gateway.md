---
title: Szolgáltatások Távoli asztali átjáró Azure Active Directory
description: Építészeti útmutató Távoli asztali átjáró szolgáltatások Azure Active Directorysal való eléréséhez.
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
ms.openlocfilehash: 57466cccb71444d5711a9221c324a107757c5e82
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576795"
---
# <a name="remote-desktop-gateway-services"></a>Távoli asztali átjáró szolgáltatások

A standard Távoli asztali szolgáltatások (RDS) központi telepítése a Windows Serveren futó különböző [Távoli asztal szerepkör-szolgáltatásokat](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) tartalmazza. Az Azure Active Directory (Azure AD) alkalmazásproxy-alapú üzembe helyezése állandó kimenő kapcsolatban áll az összekötő szolgáltatást futtató kiszolgálóval. Más központi telepítések egy terheléselosztó használatával hagyják el a nyitott bejövő kapcsolatokat. Ez a hitelesítési minta lehetővé teszi, hogy több típusú alkalmazást nyújtson a helyszíni alkalmazások Távoli asztali szolgáltatások használatával történő közzétételével. Emellett az Azure AD Application Proxy használatával csökkenti az üzemelő példány támadási felületét is.

## <a name="use-when"></a>A következő esetekben használja

Meg kell adnia a távelérést, és biztosítania kell a Távoli asztali szolgáltatások üzembe helyezését előhitelesítéssel.

![építészeti diagram](./media/authentication-patterns/rdp-auth.png)

## <a name="components-of-system"></a>A System összetevői

* **Felhasználó** : az Application proxy által kiszolgált RDS elérését biztosítja.

* **Webböngésző** : az alkalmazás külső URL-címének eléréséhez a felhasználó által kommunikáló összetevő.

* **Azure ad** : hitelesíti a felhasználót. 

* **Alkalmazásproxy szolgáltatás** : fordított proxyként működik a kérésnek a felhasználótól az RDS-be való továbbításához. Az alkalmazásproxy is kényszerítheti a feltételes hozzáférési szabályzatok alkalmazását. 

* **Távoli asztali szolgáltatások** : különálló virtualizált alkalmazások platformja, amely biztonságos mobil-és távoli asztali hozzáférést biztosít, és lehetővé teszi a végfelhasználók számára, hogy alkalmazásaikat és asztalait a felhőből futtassák. 

## <a name="implement-remote-desktop-gateway-services-with-azure-ad"></a>Távoli asztali átjáró szolgáltatások implementálása az Azure AD-vel

* [Távoli asztal közzététele az Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services) 

* [Helyszíni alkalmazás hozzáadása távoli eléréshez az Application proxyn keresztül az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)

 
