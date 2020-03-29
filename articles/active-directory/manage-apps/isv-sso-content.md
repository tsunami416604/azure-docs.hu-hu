---
title: Az SSO engedélyezése több-bérlős alkalmazások esetében
description: Útmutató független szoftverszállítóknak az Azure Active Directoryval való integrációhoz
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
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67795181"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>Egyszeri bejelentkezés engedélyezése a több-bérlős alkalmazáshoz  

Amikor az alkalmazást más vállalatok számára kínálja egy vásárlás vagy előfizetés révén, elérhetővé teszi az alkalmazást a saját Azure-bérlőiken belüli ügyfelek számára. Ezt úgy nevezzük, hogy több-bérlős alkalmazás létrehozása. A koncepció áttekintése: [Több-bérlős alkalmazások az Azure-ban](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) és [a Tenancy az Azure Active Directoryban.](../develop/single-and-multi-tenant-apps.md)

## <a name="what-is-single-sign-on"></a>Mi az egyszeri bejelentkezés?

Egyszeri bejelentkezés (SSO) növeli a biztonságot és a kényelmet, amikor a felhasználók az Azure Active Directory és más identitások használatával jelentkeznek be az alkalmazásokba. Ha egy alkalmazás sso engedélyezve van, a felhasználóknak nem kell megadniuk külön hitelesítő adatokat az alkalmazás eléréséhez. Az egyszeri bejelentkezés teljes magyarázata. [Lásd: Egyszeri bejelentkezés az alkalmazásokba az Azure Active Directoryban.](what-is-single-sign-on.md)

## <a name="why-enable-single-sign-on-in-your-application"></a>Miért érdemes engedélyezni az egyszeri bejelentkezést az alkalmazásban?

A több-bérlős alkalmazásban az egyszeri bejelentkezés engedélyezése számos előnye van. Ha engedélyezi az SSO-t az alkalmazáshoz:

* Az alkalmazás felsorolható az Azure Marketplace-en, ahol az alkalmazás felderíthető több millió szervezet az Azure Active Directory használatával.
  * Lehetővé teszi az ügyfelek számára, hogy gyorsan konfigurálják az alkalmazást az Azure AD-vel.

* Az alkalmazás felderíthető az Office 365 App Gallery, az Office 365 alkalmazásindítójában és a Microsoft Search on Office.com

* Az alkalmazás használhatja a Microsoft Graph REST API-t a Microsoft Graph-ból elérhető felhasználói hatékonyságot növelő adatok eléréséhez.

* A támogatási költségeket az ügyfelek számára megkönnyítve csökkentheti.
  * Az Azure AD-csapattal közösen készített alkalmazásspecifikus dokumentáció megkönnyíti a bevezetést.
  * Ha egy kattintással egyszeri bejelentkezés engedélyezve van, az ügyfelek informatikai rendszergazdáinak nem kell megtanulniuk, hogyan konfigurálhatják az alkalmazást a szervezetben való használatra.

* Lehetővé teszi az ügyfelek számára, hogy teljes mértékben kezeljék az alkalmazottés a vendégidentitások hitelesítését és engedélyezését.

  * Az identitások ügyféltulajdonosával kapcsolatos összes fiókkezelés és megfelelőségi felelősség.

  * Lehetővé teszi az sso engedélyezését vagy letiltását bizonyos identitásszolgáltatók, csoportok vagy felhasználók számára az üzleti igényeiknek megfelelően.

* Ön növeli a piacképes és elfogadható. Sok nagy szervezet megköveteli, hogy (vagy törekedjen) az alkalmazottak zökkenőmentes Egyszeri bejelentkezés élmény minden alkalmazás. Az SSO egyszerűvé tétele fontos.

* Csökkentheti a végfelhasználói súrlódást, ami növelheti a végfelhasználói használatot és növelheti a bevételt.

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>Az egyszeri bejelentkezés engedélyezése a közzétett alkalmazásban

1. [Válassza ki a megfelelő összevonási protokollt a több-bérlős alkalmazáshoz.](isv-choose-multi-tenant-federation.md)
1. Az SSO implementálja az alkalmazásban
   - Lásd [a hitelesítési mintákra vonatkozó útmutatást](../develop/v2-app-types.md)
   - Lásd: [Azure Active Directory-kódminták](../develop/sample-v2-code.md) OIDC és OAuth protokollokhoz
1. [Hozza létre az Azure Tenantt,](isv-tenant-multi-tenant-app.md) és tesztelje az alkalmazást
1. [Hozzon létre és tegyen közzé SSO dokumentációt a webhelyén.](isv-create-sso-documentation.md)
1. [Küldje el alkalmazáslistáját,](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) és lépjen kapcsolatba a Microsofttal, hogy dokumentációt hozzon létre a Microsoft webhelyén.
1. [Csatlakozzon a Microsoft Partner Networkhöz (ingyenes), és készítse el a piacra jutási tervet.](https://partner.microsoft.com/en-us/explore/commercial#gtm)
