---
title: Csatolt bejelentkezés az Azure AD-alkalmazásokhoz – Microsoft Identity platform
description: Társított egyszeri bejelentkezés (SSO) konfigurálása az Azure AD vállalati alkalmazásokhoz a Microsoft Identity platformon (Azure AD)
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
ms.openlocfilehash: 2c4547bddeea8b67bd3377124b1c299662fea3e6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274177"
---
# <a name="configure-linked-sign-on"></a>Csatolt bejelentkezés konfigurálása

Katalógus vagy nem katalógusos webalkalmazás hozzáadásakor az egyik elérhető egyszeri bejelentkezési lehetőség a [csatolt bejelentkezés](what-is-single-sign-on.md). Ezzel a beállítással adhat hozzá hivatkozást az alkalmazáshoz a szervezet Azure AD hozzáférési paneljén vagy az Office 365-portálon. Ezzel a módszerrel olyan egyéni webalkalmazásokra mutató hivatkozásokat adhat hozzá, amelyek jelenleg Active Directory összevonási szolgáltatások (AD FS) (vagy más összevonási szolgáltatást) használnak az Azure AD helyett a hitelesítéshez. Vagy hozzáadhat olyan mély hivatkozásokat is bizonyos SharePoint-lapokhoz vagy más weblapokhoz, amelyeket csak szeretne megjeleníteni a felhasználó hozzáférési panelén.

## <a name="before-you-begin"></a>Előkészületek

Ha az alkalmazás nem lett hozzáadva az Azure AD-bérlőhöz, tekintse meg [a Gallery-alkalmazás hozzáadása](add-gallery-app.md) vagy [a nem Gallery-alkalmazás hozzáadása](add-non-gallery-app.md)című témakört.

### <a name="open-the-app-and-select-linked-sign-on"></a>Nyissa meg az alkalmazást, és válassza a csatolt bejelentkezés lehetőséget.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) Felhőbeli alkalmazás-rendszergazdaként vagy az Azure ad-bérlőhöz tartozó alkalmazás-rendszergazdaként.

1. Navigáljon **Azure Active Directory** > **vállalati alkalmazások**elemre. Megjelenik az Azure AD-bérlőben található alkalmazások véletlenszerű mintája. 

1. Az **alkalmazás típusa** menüben válassza a **minden alkalmazás**lehetőséget, majd kattintson az **alkalmaz**gombra.

1. Adja meg az alkalmazás nevét a keresőmezőbe, majd válassza ki az alkalmazást az eredmények közül.

1. A **kezelés** szakaszban válassza az **egyszeri bejelentkezés**lehetőséget. 

1. Válassza a **csatolt**lehetőséget.

1. Adja meg az alkalmazáshoz csatolni kívánt URL-címet. Írja be az URL-címet, majd válassza a **Mentés**lehetőséget. 
 
1. A felhasználókat és csoportokat hozzárendelhet az alkalmazáshoz, ami azt eredményezi, hogy az alkalmazás az [Office 365 app launcherben](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) vagy az [Azure ad hozzáférési paneljén](end-user-experiences.md) jelenik meg a felhasználók számára.

1. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók vagy csoportok társítása az alkalmazáshoz](methods-for-assigning-users-and-groups.md)
- [A felhasználói fiókok automatikus üzembe helyezésének konfigurálása](configure-automatic-user-provisioning-portal.md)
