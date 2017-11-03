---
title: "Megtudhatja, ha egy adott felhasználó tudnak csatlakozni egy alkalmazáshoz |} Microsoft Docs"
description: "Ha egy különösen fontos felhasználói tudnak csatlakozni egy alkalmazáshoz, a felhasználók átadása az Azure ad szolgáltatással konfigurált megállapítása"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: fcefb31904cfb77022db0358e9feee6a0479db81
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Annak megállapítása, ha egy adott felhasználó tudnak csatlakozni egy alkalmazáshoz
Ha automatikus felhasználólétesítés alkalmazással, az Azure AD létesítése és a frissítés felhasználói fiókokat az alkalmazáson belüli alapján automatikusan többek között a [felhasználók és csoportok hozzárendelése](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) időközönként rendszeresen ütemezett, általában 10 percenként.

## <a name="how-long-does-it-take"></a>Mennyi időt vesz igénybe?

Úgy kell létrehozni, az adott felhasználó számára szükséges idő elsősorban attól függ-e már megtörtént egy kezdeti "teljes" szinkronizálást.

Az első szinkronizálás az Azure AD között és az alkalmazások is igénybe vehet 20 percet az Azure AD-címtár és a felhasználók kialakítási hatókörében számát méretétől függően több órát. 

A kezdeti szinkronizálás után az ezt követő szinkronizálások gyorsabb lehet (pl. 10 percen belül), szerint a létesítési szolgáltatás, amelyben a kezdeti szinkronizálást, és ezt követő szinkronizálások teljesítményének javítása után mindkét állapota egy vízjelek tárolja.

## <a name="how-to-check-the-status-of-a-user"></a>A felhasználó állapotának ellenőrzése

A kijelölt felhasználó a kiépítési állapotát tekintheti meg, tekintse meg a felügyeleti naplók az Azure ad-ben.

A telepítési naplók érhetők el az Azure portálon, a **Azure Active Directory &gt; vállalati alkalmazások &gt; \[alkalmazásnév\] &gt; vizsgálati naplókban** fülre. A naplók szűrést végezni a **fiók** kategóriát csak az adott alkalmazáshoz létesítési események megjelenítéséhez. A "egyező ID" attribútum-leképezésekhez számukra konfigurált alapján is kereshet. 

Például, ha az "egyszerű felhasználónév" vagy "e-mail cím" az Azure AD-oldalán a megfelelő attribútumaként konfigurálva, és a felhasználó nem kiépítés értéke "audrey@contoso.com", majd keresse meg a naplókban talál "audrey@contoso.com", és tekintse át ezt követően eredményt adott vissza.

A telepítési naplók jegyezze fel a létesítési szolgáltatás által végzett összes műveletet többek között:

* Azure ad-val történő üzembe helyezéséhez hatókörébe hozzárendelt felhasználók lekérdezése
* A cél alkalmazás meglétét azoknak a felhasználóknak az lekérdezése
* A felhasználói objektum, a rendszer közötti összehasonlítása
* Hozzáadása, frissítése vagy a felhasználói fiók letiltása a célrendszeren, az összehasonlítás alapján

## <a name="next-steps"></a>Következő lépések
[Felhasználói kiépítésének és megszüntetésének biztosítása SaaS-alkalmazásokhoz az Azure Active Directoryval történő automatizálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)".
