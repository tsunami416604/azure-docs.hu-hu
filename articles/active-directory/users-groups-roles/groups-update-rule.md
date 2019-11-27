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
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c387e2d78adcaebc430073a2a45818c4a0928b9f
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422354"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Dinamikus csoport frissítése Azure Active Directory tagságának kezeléséhez

Azure Active Directory (Azure AD) esetében a szabályok segítségével meghatározhatja a csoporttagság alapján a felhasználók vagy az eszközök tulajdonságait. Ez a cikk azt ismerteti, hogyan lehet szabályt beállítani egy dinamikus csoporthoz a Azure Portalban.
A dinamikus tagság biztonsági csoportok vagy Office 365-csoportok esetén támogatott. Csoporttagság-szabály alkalmazása esetén a rendszer kiértékeli a felhasználók és az eszközök attribútumait a tagsági szabállyal való egyezések alapján. Amikor egy attribútum megváltoztatja a felhasználót vagy az eszközt, a rendszer a szervezet összes dinamikus csoportjának szabályait dolgozza fel a tagság változásaihoz. A felhasználók és az eszközök akkor lesznek hozzáadva vagy eltávolítva, ha megfelelnek a csoport feltételeinek.

## <a name="rule-builder-in-the-azure-portal"></a>Szabálykészlet a Azure Portalban

Az Azure AD egy olyan szabályt biztosít, amellyel gyorsabban hozhat létre és frissíthet fontos szabályokat. A szabály-szerkesztő legfeljebb öt kifejezést támogat. A szabály-szerkesztő megkönnyíti néhány egyszerű kifejezéssel rendelkező szabály kiépítését, azonban nem használható minden szabály újbóli létrehozásához. Ha a szabály-szerkesztő nem támogatja a létrehozni kívánt szabályt, használhatja a szövegmezőt.

Íme néhány példa a speciális szabályokra vagy szintaxisra, amelyekhez ajánlott a következő szövegmezővel létrehozni:

- Szabály ötnél több kifejezéssel
- A közvetlen jelentések szabálya
- [Operátor prioritásának](groups-dynamic-membership.md#operator-precedence) beállítása
- [Összetett kifejezésekkel rendelkező szabályok](groups-dynamic-membership.md#rules-with-complex-expressions); például `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Előfordulhat, hogy a szabály-szerkesztő nem tudja megjeleníteni a szövegmezőben létrehozott egyes szabályokat. Előfordulhat, hogy egy üzenet jelenik meg, ha a szabály-szerkesztő nem tudja megjeleníteni a szabályt. A szabály-szerkesztő semmilyen módon nem módosítja a dinamikus csoport szabályainak támogatott szintaxisát, érvényesítését vagy feldolgozását.

![Tagsági szabály hozzáadása dinamikus csoporthoz](./media/groups-update-rule/update-dynamic-group-rule.png)

A tagsági szabályok szintaxisára, támogatott tulajdonságaira, operátorára és értékeire példákat a következő témakörben talál: [Azure Active Directory csoportok dinamikus tagsági szabályai](groups-dynamic-membership.md).

## <a name="to-update-a-group-membership-rule"></a>Csoporttagság-szabály frissítése

1. Jelentkezzen be az [Azure ad felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a globális rendszergazda, csoport rendszergazdája, Intune-rendszergazda vagy felhasználói rendszergazdai szerepkör tagja a bérlőn.
1. Válassza a **csoportok** > **az összes csoport**elemet.
1. Válasszon ki egy csoportot a profiljának megnyitásához.
1. A csoport profil lapján válassza a **dinamikus tagsági szabályok**lehetőséget. A szabály-szerkesztő legfeljebb öt kifejezést támogat. Ötnél több kifejezés hozzáadásához a szövegmezőt kell használnia.

   ![Tagsági szabály hozzáadása dinamikus csoporthoz](./media/groups-update-rule/update-dynamic-group-rule.png)

1. A tagsági szabályhoz elérhető egyéni kiterjesztési tulajdonságok megtekintéséhez:
   1. Válassza az **Egyéni bővítmény tulajdonságainak beolvasása** elemet.
   1. Adja meg az alkalmazás AZONOSÍTÓját, majd kattintson a **Tulajdonságok frissítése**elemre.
1. A szabály frissítése után válassza a **Mentés**lehetőséget.

Ha a beírt szabály nem érvényes, a portálon egy Azure-értesítésben láthatja, hogy a szabály miért nem dolgozható fel. Olvassa el figyelmesen, hogy megtudja, hogyan javíthatja a szabályt.

## <a name="check-processing-status-for-a-rule"></a>Szabály feldolgozási állapotának keresése

A csoport **Áttekintés** lapján megtekintheti a tagság feldolgozási állapotát és a legutóbbi frissítés dátumát.
  
  ![a dinamikus csoport állapotának megjelenítése](./media/groups-create-rule/group-status.png)

A következő állapotüzenetek láthatók a **tagság feldolgozási** állapotához:

- **Értékelés**: a csoport módosítása megérkezett, és a frissítések kiértékelése folyamatban van.
- **Feldolgozás**: folyamatban van a frissítések feldolgozása.
- A **frissítés befejeződött**: a feldolgozás befejeződött, és a rendszer az összes vonatkozó frissítést elvégezte.
- **Feldolgozási hiba**: a feldolgozás nem fejeződött be, mert hiba történt a tagsági szabály kiértékelése során.
- A **frissítés szüneteltetve**: a dinamikus tagsági szabály frissítéseit a rendszergazda szünetelteti. A MembershipRuleProcessingState "szüneteltetve" értékre van állítva.

A következő állapotüzenetek jeleníthetők meg a **tagság utolsó frissítésének** állapotához:

- **Dátum és idő**: a tagság utolsó frissítésekor.
- **Folyamatban**: a frissítések jelenleg folyamatban vannak.
- **Ismeretlen**: az utolsó frissítés időpontja nem olvasható be. Lehet, hogy a csoport új.

Ha hiba lép fel egy adott csoport tagsági szabályának feldolgozása közben, a rendszer riasztást jelenít meg a csoport **Áttekintés oldalának** tetején. Ha a bérlőn belüli összes csoportra vonatkozóan még 24 óráig nem dolgozható fel a függőben lévő dinamikus tagsági frissítések, a rendszer az **összes csoport**tetején riasztást jelenít meg.

![hibaüzenetek feldolgozása – riasztások](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>Következő lépések

Ezek a cikkek további információkat nyújtanak a dinamikus csoportok Azure AD-beli használatáról.

- A dinamikus szabályok struktúrájára vonatkozó teljes körű hivatkozásért lásd: [dinamikus tagsági szabály szintaxisa](groups-dynamic-membership.md).
- [Hozzon létre egy statikus tagsági csoportot, és vegyen fel tagokat](../fundamentals/active-directory-groups-create-azure-portal.md).
