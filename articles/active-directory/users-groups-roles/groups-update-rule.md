---
title: Dinamikus csoport szabályának frissítése és kezelése – Azure Active Directory | Microsoft Docs
description: Csoporttagság-szabály létrehozása a Azure Portalban, ellenőrzési állapot.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/12/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce3bce5e2656efea0a4fc3d7aa6be46f1e6926ec
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657345"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Dinamikus csoport frissítése Azure Active Directory tagságának kezeléséhez

Azure Active Directory (Azure AD) esetében a szabályok segítségével meghatározhatja a csoporttagság alapján a felhasználók vagy az eszközök tulajdonságait. Ez a cikk azt ismerteti, hogyan lehet szabályt beállítani egy dinamikus csoporthoz a Azure Portalban.
A dinamikus tagság biztonsági csoportok vagy Office 365-csoportok esetén támogatott. Csoporttagság-szabály alkalmazása esetén a rendszer kiértékeli a felhasználók és az eszközök attribútumait a tagsági szabállyal való egyezések alapján. Amikor egy attribútum megváltoztatja a felhasználót vagy az eszközt, a rendszer a szervezet összes dinamikus csoportjának szabályait dolgozza fel a tagság változásaihoz. A felhasználók és az eszközök akkor lesznek hozzáadva vagy eltávolítva, ha megfelelnek a csoport feltételeinek.

A tagsági szabályok szintaxisára, támogatott tulajdonságaira, operátorára és értékeire példákat a következő témakörben talál: [Azure Active Directory csoportok dinamikus tagsági szabályai](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Csoporttagság-szabály frissítése

1. Jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com) központba egy olyan fiókkal, amely a bérlő globális rendszergazdája, Intune-rendszergazdája vagy felhasználói rendszergazdai szerepköre.
1. Válassza a **csoportok** > **minden csoport**elemet.
1. Válasszon ki egy csoportot a profiljának megnyitásához.
1. A csoport profil lapján válassza a **dinamikus tagsági szabályok**lehetőséget. A szabály-szerkesztő legfeljebb öt kifejezést támogat. Hatodik vagy bármely további kifejezés hozzáadásához a szövegmezőt kell használnia.

   ![Tagsági szabály hozzáadása dinamikus csoporthoz](./media/groups-update-rule/update-dynamic-group-rule.png)

1. A tagsági szabályhoz elérhető egyéni kiterjesztési tulajdonságok megtekintéséhez:
   1. Válassza az **Egyéni bővítmény tulajdonságainak** beolvasása elemet.
   2. Adja meg az alkalmazás AZONOSÍTÓját, majd kattintson a **Tulajdonságok frissítése**elemre.
1. A szabály frissítése után válassza a **Mentés**lehetőséget.

Ha a beírt szabály nem érvényes, akkor a portálon az Azure-értesítésben a szabály nem dolgozható fel. Olvassa el figyelmesen, hogy megtudja, hogyan javíthatja a szabályt.

## <a name="check-processing-status-for-a-rule"></a>Szabály feldolgozási állapotának keresése

A csoport **Áttekintés** lapján megtekintheti a tagság feldolgozási állapotát és a legutóbbi frissítés dátumát.
  
  ![a dinamikus csoport állapotának megjelenítése](./media/groups-create-rule/group-status.png)

A következő állapotüzenetek láthatók a **tagság feldolgozási** állapotához:

* **Értékelés**:  A csoport módosítása megérkezett, és a frissítések kiértékelése folyamatban van.
* **Feldolgozás**: A frissítések feldolgozása folyamatban van.
* A **frissítés befejeződött**: A feldolgozás befejeződött, és az összes vonatkozó frissítés megtörtént.
* **Feldolgozási hiba**:  Nem sikerült befejezni a feldolgozást, mert hiba történt a tagsági szabály kiértékelése során.
* **Frissítés szüneteltetve**: A dinamikus tagsági szabály frissítéseit a rendszergazda szünetelteti. A MembershipRuleProcessingState "szüneteltetve" értékre van állítva.

A következő állapotüzenetek jeleníthetők meg a **tagság utolsó frissítésének** állapotához:

* **Dátum és idő**: A tagság utolsó frissítésének időpontja.
* **Folyamatban**: A frissítések jelenleg folyamatban vannak.
* **Ismeretlen**: A legutóbbi frissítés időpontja nem olvasható be. Lehet, hogy a csoport új.

Ha hiba lép fel egy adott csoport tagsági szabályának feldolgozása közben, a rendszer riasztást jelenít meg a csoport **Áttekintés oldalának** tetején. Ha a bérlőn belüli összes csoportra vonatkozóan még 24 óráig nem dolgozható fel a függőben lévő dinamikus tagsági frissítések, a rendszer az **összes csoport**tetején riasztást jelenít meg.

![hibaüzenetek feldolgozása – riasztások](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>További lépések

Ezek a cikkek további információkat nyújtanak a dinamikus csoportok Azure AD-beli használatáról.

* A dinamikus szabályok struktúrájára vonatkozó teljes körű hivatkozásért lásd: [dinamikus tagsági szabály szintaxisa](groups-dynamic-membership.md).
* [Hozzon létre egy statikus tagsági csoportot, és vegyen fel tagokat](../fundamentals/active-directory-groups-create-azure-portal.md).
