---
title: Ismerje meg, ha egy adott felhasználó fog tudni hozzáférni egy alkalmazáshoz |} A Microsoft Docs
description: Hogyan tudhatja meg, ha egy kritikus fontosságú felhasználói férhetnek hozzá a felhasználókiépítés Azure AD-val konfigurált alkalmazás
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 94b7ade4452f760940d545829c71d3b81426467a
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363060"
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Ismerje meg, ha egy adott felhasználó fog tudni hozzáférni egy alkalmazáshoz
Felhasználók automatikus átadása az alkalmazás használatakor az Azure AD-alkalmazás üzembe helyezése és frissítése felhasználói fiókok alapján automatikusan többek között [felhasználó és csoport-hozzárendelések](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) rendszeresen ütemezett időintervallum, általában minden 10 perc.

## <a name="how-long-does-it-take"></a>Mennyi időt vesz igénybe?

Ki kell építeni az adott felhasználó számára szükséges idő elsősorban attól függ-e már megtörtént egy kezdeti "teljes" szinkronizálást.

Az első szinkronizálás az Azure AD között és a egy alkalmazást is igénybe vehet 20 perc alatt az Azure AD-címtár és a kiépítés hatókörébe felhasználók méretétől függően több órát. 

A kezdeti szinkronizálást követően ezt követő szinkronizálások lehet gyorsabb (pl. 10 percen belül), mivel a kiépítési szolgáltatás tárolja, amely mindkét rendszer állapotát képviselik a ezt követő szinkronizálások teljesítményének növelése a kezdeti szinkronizálást követően a vízjelek.

## <a name="how-to-check-the-status-of-a-user"></a>A felhasználó állapotának ellenőrzése

Tekintse meg a kiválasztott felhasználóról a kiépítési állapotot, tekintse meg a naplók az Azure ad-ben.

Az üzembe helyezési naplók elérhetők az Azure Portalon a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt; Auditnaplók** fülre. A naplók szűrése a **fiók üzembe helyezésének** kategóriát csak az alkalmazás az üzembe helyezési események megtekintéséhez. A felhasználók számára a "egyező azonosító", amelyet a számukra az attribútumleképezések alapján kereshet. 

Például, ha konfigurálta a "felhasználó egyszerű neve" vagy "e-mail-cím", a megfelelő attribútum az Azure ad-ben oldalán, és a felhasználó nem kiépítése, amelynek értéke "audrey@contoso.com", majd keresse meg a naplófájlok "audrey@contoso.com" majd tekintse át a bejegyzéseket, és adott vissza.

Az üzembe helyezési naplók jegyezze fel a kiépítési szolgáltatás által végrehajtott összes műveletet többek között:

* Az Azure AD-hez hozzárendelt felhasználók a kiépítés hatókörébe lekérdezése
* Ezek a felhasználók létezik-e a célalkalmazásnak lekérdezése
* A felhasználói objektumok között a rendszer összehasonlítása
* Hozzáadása, frissítése vagy a felhasználói fiók letiltása a célrendszeren, az összehasonlítás alapján

## <a name="next-steps"></a>További lépések
[Automatizálhatja a felhasználói kiépítésének és megszüntetésének biztosítása az SaaS-alkalmazásokhoz az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)''
