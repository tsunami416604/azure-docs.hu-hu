---
title: Csatolt bejelentkezés az Azure AD-alkalmazásokhoz – Microsoft identitásplatform
description: Összekapcsolt egyszeri bejelentkezés (SSO) konfigurálása az Azure AD nagyvállalati alkalmazásaihoz a Microsoft identity platformon (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063543"
---
# <a name="configure-linked-sign-on"></a>Csatolt bejelentkezés konfigurálása

Amikor hozzáad egy galériát vagy nem galéria alapú webalkalmazást, az egyszeri bejelentkezési lehetőségek egyike a [csatolt bejelentkezés.](what-is-single-sign-on.md) Válassza ezt a lehetőséget, ha a szervezet Azure AD Access Panelvagy office 365-ös portálján szeretne egy hivatkozást hozzáadni az alkalmazáshoz. Ezzel a módszerrel az Azure AD helyett az Active Directory összevonási szolgáltatásokat (vagy más összevonási szolgáltatást) használó egyéni webalkalmazásokhoz adhat hozzá hivatkozásokat. Másik lehetőségként hozzáadhat mélyhivatkozásokat bizonyos SharePoint-lapokhoz vagy más weblapokhoz, amelyeket csak meg szeretne jelenadni a felhasználó hozzáférési paneljein.

## <a name="before-you-begin"></a>Előkészületek

Ha az alkalmazás még nem lett hozzáadva az Azure AD-bérlőhöz, olvassa el [a Katalógusalkalmazás hozzáadása](add-gallery-app.md) vagy a Nem galériaalkalmazás hozzáadása című [témakört.](add-non-gallery-app.md)

### <a name="open-the-app-and-select-linked-sign-on"></a>Az alkalmazás megnyitása és a csatolt bejelentkezés kiválasztása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy felhőalapú alkalmazás-rendszergazdaként, vagy egy alkalmazás-rendszergazda az Azure AD-bérlő.

1. Nyissa meg az **Azure Active Directory** > **Enterprise alkalmazásokat.** Az Azure AD-bérlőben lévő alkalmazások véletlenszerű mintája jelenik meg. 

1. Az **Alkalmazás típusa** menüben válassza a **Minden alkalmazás**lehetőséget, majd az **Alkalmaz parancsot.**

1. Írja be az alkalmazás nevét a keresőmezőbe, majd válassza ki az alkalmazást az eredmények közül.

1. A **Kezelés csoportban** válassza az **Egyszeri bejelentkezés**lehetőséget. 

1. Válassza a **Csatolt**lehetőséget.

1. Adja meg annak az alkalmazásnak az URL-címét, amelyre hivatkozni szeretne. Írja be az URL-címet, és válassza a **Mentés lehetőséget.** 
 
1. Felhasználókat és csoportokat rendelhet az alkalmazáshoz, ami azt eredményezi, hogy az alkalmazás megjelenik az [Office 365 alkalmazásindítójában](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) vagy az [Azure AD hozzáférési panelen](end-user-experiences.md) ezekhez a felhasználókhoz.

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

- [Felhasználók vagy csoportok hozzárendelése az alkalmazáshoz](methods-for-assigning-users-and-groups.md)
- [A felhasználói fiókok automatikus kiépítésének konfigurálása](../app-provisioning/configure-automatic-user-provisioning-portal.md)
