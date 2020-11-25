---
title: Rugalmasság kialakítása az alkalmazás-hozzáférés alkalmazás-proxyval való használatához
description: Útmutató az építészek és a rendszergazdák számára az alkalmazásproxy használatáról a helyszíni alkalmazásokhoz való rugalmas hozzáféréshez
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8bfc3fb239f30911eddf0aa27496a465e36c486
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919566"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>Rugalmasság kialakítása az alkalmazás-hozzáférés alkalmazás-proxyval való használatához

Az alkalmazásproxy az Azure AD egyik funkciója, amely lehetővé teszi a felhasználók számára a helyszíni webalkalmazások távoli ügyfélről való elérését. Az alkalmazásproxy a felhőben található alkalmazásproxy-szolgáltatást és az alkalmazásproxy-összekötőket is tartalmazza, amelyek egy helyszíni kiszolgálón futnak. 

A felhasználók az Application proxyn keresztül közzétett URL-címen keresztül férhetnek hozzá a helyszíni erőforrásokhoz. A rendszer átirányítja őket az Azure AD bejelentkezési oldalára. Az Azure AD alkalmazásproxy szolgáltatása ezt követően egy jogkivonatot küld a vállalati hálózatban lévő alkalmazásproxy-összekötőnek, amely átadja a jogkivonatot a helyszíni Active Directory a hitelesített felhasználó ezután hozzáférhet a helyszíni erőforráshoz. Az alábbi ábrán az [Összekötők](../manage-apps/application-proxy-connectors.md) egy [összekötő csoportban](../manage-apps/application-proxy-connector-groups.md)jelennek meg.

> [!IMPORTANT]
> Ha az Application proxyn keresztül tesz közzé alkalmazásokat, meg kell valósítania [a kapacitás megtervezését és a megfelelő redundanciát az alkalmazásproxy-összekötők számára](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning).

![Az y alkalmazás architektúrájának ábrája](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>Hogyan alkalmazásproxy implementálása?

Az Azure AD Application Proxy-vel való távelérés megvalósításához tekintse meg a következő forrásokat.

* [Alkalmazásproxy üzembe helyezésének megtervezése](../manage-apps/application-proxy-deployment-plan.md)

* [Magas rendelkezésre állás és terheléselosztás – ajánlott eljárások](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [Proxykiszolgálók konfigurálása](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [Rugalmas hozzáférés-vezérlési stratégia kialakítása](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>További lépések
Rugalmassági erőforrások rendszergazdák és építészek számára
 
* [Rugalmasság létrehozása a hitelesítőadat-kezeléssel](resilience-in-credentials.md)

* [Rugalmasság kiépítése az eszközök állapotával](resilience-with-device-states.md)

* [Rugalmasság kiépítése a folyamatos hozzáférés kiértékelésének (CAE) használatával](resilience-with-continuous-access-evaluation.md)

* [Rugalmasság létrehozása külső felhasználói hitelesítéssel](resilience-b2b-authentication.md)

* [Hozzon létre rugalmasságot a hibrid hitelesítésben](resilience-in-hybrid.md)

Rugalmassági erőforrások fejlesztők számára

* [IAM-rugalmasság létrehozása az alkalmazásokban](resilience-app-development-overview.md)

* [Hozzon létre rugalmasságot a CIAM-rendszerekben](resilience-b2c.md)
