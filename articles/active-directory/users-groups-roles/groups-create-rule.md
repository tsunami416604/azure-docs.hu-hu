---
title: Dinamikus csoport létrehozása vagy szerkesztése és állapotbekerülése - Azure AD | Microsoft dokumentumok
description: Csoporttagsági szabály létrehozása vagy frissítése az Azure Portalon, és a feldolgozási állapot ának ellenőrzése.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ed7f27e2145f666f38eec5ddc6c985a4d32138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266376"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Dinamikus csoport létrehozása vagy frissítése az Azure Active Directoryban

Az Azure Active Directoryban (Azure AD) szabályok használatával meghatározhatja a csoporttagságot a felhasználó vagy az eszköz tulajdonságai alapján. Ez a cikk bemutatja, hogyan állíthat be egy szabályt egy dinamikus csoporthoz az Azure Portalon.
A dinamikus tagság biztonsági csoportok vagy Office 365-csoportok esetén támogatott. Csoporttagsági szabály alkalmazásakor a rendszer kiértékeli a felhasználói és eszközattribútumokat a tagsági szabállyal való egyezések miatt. Amikor egy felhasználó vagy eszköz attribútuma megváltozik, a szervezet összes dinamikus csoportszabálya fellesz dolgozva a tagság változásaihoz. A felhasználók és az eszközök hozzáadásra vagy eltávolításra kerülnek, ha megfelelnek egy csoport feltételeinek. A biztonsági csoportok eszközökhöz vagy felhasználókhoz is használhatók, de az Office 365-csoportok csak felhasználói csoportok lehetnek.

## <a name="rule-builder-in-the-azure-portal"></a>Szabályszerkesztő az Azure Portalon

Az Azure AD egy szabályszerkesztőt biztosít a fontos szabályok gyorsabb létrehozásához és frissítéséhez. A szabályszerkesztő legfeljebb öt kifejezés felépítését támogatja. A szabályszerkesztő megkönnyíti a szabály néhány egyszerű kifejezéssel történő kialakítását, azonban nem használható minden szabály reprodukálására. Ha a szabályszerkesztő nem támogatja a létrehozni kívánt szabályt, használhatja a szövegdobozt.

Íme néhány példa olyan speciális szabályokra vagy szintaxisokra, amelyeket a szövegmező használatával szeretne létrehozni:

- Ötnél több kifejezéssel rendelkező szabály
- A Közvetlen jelentések szabály
- [Operátori elsőbbség](groups-dynamic-membership.md#operator-precedence) beállítása
- [Összetett kifejezésekkel rendelkező szabályok](groups-dynamic-membership.md#rules-with-complex-expressions); például`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Előfordulhat, hogy a szabályszerkesztő nem tud megjeleníteni néhány, a szövegmezőben készült szabályt. Akkor jelenhet meg üzenet, ha a szabályszerkesztő nem tudja megjeleníteni a szabályt. A szabályszerkesztő semmilyen módon nem módosítja a támogatott szintaxist, az érvényesítést vagy a dinamikus csoportszabályok feldolgozását.

![Tagsági szabály hozzáadása dinamikus csoporthoz](./media/groups-create-rule/update-dynamic-group-rule.png)

Példák a szintaxis, a támogatott tulajdonságok, operátorok és értékek egy tagsági szabály, lásd: [Dinamikus tagsági szabályok csoportok az Azure Active Directoryban.](groups-dynamic-membership.md)

## <a name="to-create-a-group-membership-rule"></a>Csoporttagsági szabály létrehozása

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a globális rendszergazda, az Intune-rendszergazda vagy a felhasználói rendszergazdai szerepkör a bérlőben.
1. Keresse meg és válassza a **Csoportok lehetőséget.**
1. Jelölje be **az Összes csoport**lehetőséget, majd az Új **csoport**lehetőséget.

   ![Új csoport hozzáadásához válassza ki a parancsot](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. A **Csoport** lapon adja meg az új csoport nevét és leírását. Válasszon **egy tagságtípust** a felhasználókhoz vagy az eszközökhöz, majd válassza **a Dinamikus lekérdezés hozzáadása**lehetőséget. A szabályszerkesztő legfeljebb öt kifejezést támogat. Ha ötnél több kifejezést szeretne hozzáadni, a szövegdobozt kell használnia.

   ![Tagsági szabály hozzáadása dinamikus csoporthoz](./media/groups-create-rule/add-dynamic-group-rule.png)

1. A tagsági lekérdezéshez elérhető egyéni bővítménytulajdonságok megtekintése:
   1. Az **Egyéni bővítmény tulajdonságainak beszereznie** lehetőséget
   1. Írja be az alkalmazásazonosítót, majd válassza **a Tulajdonságok frissítése lehetőséget.**
1. A szabály létrehozása után válassza a **Mentés gombot.**
1. A csoport létrehozásához válassza a **Létrehozás** gombot az **Új csoport** lapon.

Ha a megadott szabály nem érvényes, a szabály feldolgozásának oka ként megjelenő magyarázat megjelenik a portálon az Azure-értesítésben. Olvassa el figyelmesen, hogy megértsék, hogyan kell kijavítani a szabályt.

## <a name="to-update-an-existing-rule"></a>Meglévő szabály frissítése

1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a globális rendszergazda, a csoport rendszergazdája, az Intune-rendszergazda vagy a felhasználói rendszergazdai szerepkör a bérlőben.
1. Válassza **a Csoportok** > **minden csoport lehetőséget.**
1. Jelöljön ki egy csoportot a profiljának megnyitásához.
1. A csoport profillapján válassza a **Dinamikus tagsági szabályok**lehetőséget. A szabályszerkesztő legfeljebb öt kifejezést támogat. Ha ötnél több kifejezést szeretne hozzáadni, a szövegdobozt kell használnia.

   ![Tagsági szabály hozzáadása dinamikus csoporthoz](./media/groups-create-rule/update-dynamic-group-rule.png)

1. A tagsági szabályhoz elérhető egyéni bővítménytulajdonságok megtekintése:
   1. Az **Egyéni bővítmény tulajdonságainak beszereznie** lehetőséget
   1. Írja be az alkalmazásazonosítót, majd válassza **a Tulajdonságok frissítése lehetőséget.**
1. A szabály frissítése után válassza a **Mentés gombot.**

## <a name="turn-on-or-off-welcome-email"></a>Az üdvözlő e-mailek be- és kikapcsolása

Új Office 365-csoport létrehozásakor a rendszer üdvözlő e-mail értesítést küld a csoporthoz hozzáadott felhasználóknak. Később, ha egy felhasználó vagy eszköz bármely attribútuma megváltozik, a szervezet összes dinamikus csoportszabályát feldolgozza a rendszer a tagság módosításához. A hozzáadott felhasználók is megkapják az üdvözlő értesítést. Ezt a viselkedést az [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)ben kikapcsolhatja.

## <a name="check-processing-status-for-a-rule"></a>Szabály feldolgozási állapotának ellenőrzése

A csoport **áttekintő** lapján megtekintheti a tagság feldolgozási állapotát és az utolsó frissített dátumot.
  
  ![dinamikus csoport állapotának megjelenítése](./media/groups-create-rule/group-status.png)

A **tagság feldolgozási** állapotához a következő állapotüzenetek jeleníthetők meg:

- **Kiértékelés**: A csoportmódosítás beérkezett, és a frissítések kiértékelése folyamatban van.
- **Feldolgozás**: A frissítések feldolgozása folyamatban van.
- **Frissítés kész**: A feldolgozás befejeződött, és az összes vonatkozó frissítés megtörtént.
- **Feldolgozási hiba:** A feldolgozás nem fejezhető be, mert hiba történt a tagsági szabály kiértékelése kor.
- **Frissítés szüneteltetve**: A rendszergazda szüneteltette a dinamikus tagsági szabályok frissítéseit. A MembershipRuleProcessingState beállítása "Szünetelt".

A tagság utolsó frissített állapotához a következő állapotüzenetek jeleníthetők **meg:**

- &lt;**Dátum és idő**&gt;: A tagság legutóbbi frissítése.
- **Folyamatban:** A frissítések jelenleg folyamatban vannak.
- **Ismeretlen**: Az utolsó frissítés időpontja nem olvasható be. Lehet, hogy a csoport új.

Ha egy adott csoport tagsági szabályának feldolgozása közben hiba történik, egy riasztás jelenik meg a csoport **Áttekintés lapjának** tetején. Ha a bérlőn belül több mint 24 órán keresztül nem lehet függőben lévő dinamikus tagsági frissítéseket feldolgozni, akkor egy figyelmeztetés jelenik meg az **Összes csoport**tetején.

![feldolgozás, hibaüzenetek riasztásai](./media/groups-create-rule/processing-error.png)

Ezek a cikkek további információkat nyújtanak az Azure Active Directory csoportokról.

- [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Új csoport létrehozása és tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
