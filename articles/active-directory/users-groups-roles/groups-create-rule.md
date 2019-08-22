---
title: Dinamikus csoport létrehozása és állapotának ellenõrzése – Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: cb4f9d2f78857231d0ecd81a2538a75b4b8a2f74
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650303"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Dinamikus csoport létrehozása és az állapot ellenõrzése

Azure Active Directory (Azure AD) esetében a szabályok segítségével meghatározhatja a csoporttagság alapján a felhasználók vagy az eszközök tulajdonságait. Ez a cikk azt ismerteti, hogyan lehet szabályt beállítani egy dinamikus csoporthoz a Azure Portalban.
A dinamikus tagság biztonsági csoportok vagy Office 365-csoportok esetén támogatott. Csoporttagság-szabály alkalmazása esetén a rendszer kiértékeli a felhasználók és az eszközök attribútumait a tagsági szabállyal való egyezések alapján. Amikor egy attribútum megváltoztatja a felhasználót vagy az eszközt, a rendszer a szervezet összes dinamikus csoportjának szabályait dolgozza fel a tagság változásaihoz. A felhasználók és az eszközök akkor lesznek hozzáadva vagy eltávolítva, ha megfelelnek a csoport feltételeinek. A biztonsági csoportok akár eszközökhöz, akár felhasználókhoz is felhasználhatók, de az Office 365-csoportok csak felhasználói csoportok lehetnek.

A tagsági szabályok szintaxisára, támogatott tulajdonságaira, operátorára és értékeire példákat a következő témakörben talál: [Azure Active Directory csoportok dinamikus tagsági szabályai](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Csoporttagság-szabály létrehozása

1. Jelentkezzen be az [Azure ad felügyeleti](https://aad.portal.azure.com) központba egy olyan fiókkal, amely a bérlő globális rendszergazdája, Intune-rendszergazdája vagy felhasználói rendszergazdai szerepköre.
2. Válassza a **csoportok**lehetőséget.
3. Válassza ki **az összes csoportot**, és válassza az **új csoport**lehetőséget.

   ![Válassza ki az új csoport hozzáadására szolgáló parancsot](./media/groups-create-rule/new-group-creation.png)

4. A **csoport** lapon adja meg az új csoport nevét és leírását. Válasszon **tagsági típust** a felhasználók vagy eszközök számára, majd válassza a **dinamikus lekérdezés hozzáadása**lehetőséget. A szabály-szerkesztő legfeljebb öt kifejezést támogat. Hatodik vagy bármely további kifejezés hozzáadásához a szövegmezőt kell használnia.

   ![Tagsági szabály hozzáadása dinamikus csoporthoz](./media/groups-create-rule/add-dynamic-group-rule.png)

5. A tagsági lekérdezéshez elérhető egyéni kiterjesztési tulajdonságok megtekintéséhez
   1. Válassza az **Egyéni bővítmény tulajdonságainak** beolvasása elemet.
   2. Adja meg az alkalmazás AZONOSÍTÓját, majd kattintson a **Tulajdonságok frissítése**elemre.
6. A szabály létrehozása után válassza a **Mentés**lehetőséget.
7. Válassza a **Létrehozás** lehetőséget az **új csoport** lapon a csoport létrehozásához.

Ha a beírt szabály nem érvényes, akkor a portálon az Azure-értesítésben a szabály nem dolgozható fel. Olvassa el figyelmesen, hogy megtudja, hogyan javíthatja a szabályt.

## <a name="turn-on-or-off-welcome-email"></a>Üdvözlő e-mailek be-és kikapcsolása

Új Office 365-csoport létrehozásakor a rendszer egy üdvözlő e-mail-értesítést küld a csoportba felvett felhasználóknak. Később, ha a felhasználó vagy az eszköz bármelyik attribútuma módosul, a rendszer a szervezet összes dinamikus csoportjának szabályait dolgozza fel a tagság változásaihoz. A hozzáadott felhasználók megkapják az üdvözlő értesítést is. Ezt a viselkedést az [Exchange PowerShellben](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)is kikapcsolhatja.

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

* &lt;**Dátum és idő**&gt;: A tagság utolsó frissítésének időpontja.
* **Folyamatban**: A frissítések jelenleg folyamatban vannak.
* **Ismeretlen**: A legutóbbi frissítés időpontja nem olvasható be. Lehet, hogy a csoport új.

Ha hiba lép fel egy adott csoport tagsági szabályának feldolgozása közben, a rendszer riasztást jelenít meg a csoport **Áttekintés oldalának** tetején. Ha a bérlőn belüli összes csoportra vonatkozóan még 24 óráig nem dolgozható fel a függőben lévő dinamikus tagsági frissítések, a rendszer az **összes csoport**tetején riasztást jelenít meg.

![hibaüzenetek feldolgozása – riasztások](./media/groups-create-rule/processing-error.png)

Ezek a cikkek további információkat nyújtanak Azure Active Directory csoportjairól.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Új csoport létrehozása és tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
