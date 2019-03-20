---
title: Dinamikus csoport létrehozása és állapotának – Azure Active Directory |} A Microsoft Docs
description: Hogyan csoporttagsági szabály létrehozása az Azure Portalon, az állapot ellenőrzése.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f828ff83e6b9c60eb08edef7f47e88185fb5aef8
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199681"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Dinamikus csoport létrehozása és állapotának ellenőrzése

Az Azure Active Directoryban (Azure AD) a szabályok segítségével határozza meg a csoport tagságának a felhasználó vagy az eszköz tulajdonságok alapján. Ez a cikk bemutatja, hogyan állítható be a szabály a dinamikus csoportot az Azure Portalon.
A dinamikus tagsági biztonsági vagy Office 365-csoportok esetében támogatott. Egy csoporttagsági szabály alkalmazásakor a tagsági szabály egyezése a felhasználó és az eszközattribútumok értékeli ki. Attribútuma megváltozik egy felhasználóhoz vagy eszközhöz, ha a szervezet összes dinamikus csoport szabályok feldolgozása a tagságot érintő változások. Felhasználók és eszközök hozzáadásakor vagy eltávolításakor, ha azok megfelelnek a feltételeknek, egy csoportra.

A szintaxis, támogatott tulajdonságok, operátorok és a tagsági szabály értékeit példákért lásd [dinamikus tagsági szabályok az Azure Active Directoryban csoportok](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>A csoporttagsági szabály létrehozása

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely a globális rendszergazdai, Intune-rendszergazda vagy felhasználói rendszergazda szerepkör a bérlőben.
2. Válassza ki **csoportok**.
3. Válassza ki **összes csoport**, és válassza ki **új csoport**.

   ![Válassza ki a parancsot az új csoport hozzáadásához](./media/groups-create-rule/new-group-creation.png)

4. Az a **csoport** lap, adja meg egy nevet és leírást az új csoport. Válassza ki a **tagságtípusának** felhasználók vagy eszközök, és válassza ki a **dinamikus lekérdezés hozzáadása**. A szabály builder segítségével hozhat létre egy egyszerű szabályt vagy [saját magának egy tagsági szabály írása](groups-dynamic-membership.md).

   ![Dinamikus csoport tagsági szabály hozzáadása](./media/groups-create-rule/add-dynamic-group-rule.png)

5. A tagsági lekérdezést rendelkezésre álló egyéni bővítmény tulajdonságok megtekintéséhez
   1. Válassza ki **egyéni bővítménytulajdonság lekérése**
   2. Adja meg az Alkalmazásazonosítót, és válassza ki **tulajdonságainak frissítése**. 
6. A szabály létrehozása után válassza ki a **lekérdezés hozzáadása** a panel alján.
7. Válassza ki **létrehozás** a a **csoport** panelen a csoport létrehozásához.

Ha a megadott szabály nem érvényes, annak magyarázatát, hogy miért nem sikerült feldolgozni a szabályt a portál jobb felső sarokban jelenik meg. Olvassa el, hogy alaposan megismerheti, hogyan háríthatja el a szabályt.

## <a name="turn-on-or-off-welcome-email"></a>Be- és kikapcsolása üdvözlő e-mailben

Amikor létrejön egy új Office 365-csoportot, egy üdvözlő értesítést küld a felhasználókat, akik a csoportba kerülnek. Később Ha módosítja olyan felhasználó vagy eszköz attribútumai, a szervezet összes dinamikus csoportszabályok dolgozzák fel a tagság megváltozása. Felhasználók, akik kerülnek majd is kapnak az üdvözlő értesítést. Kapcsolhatja ki ezt a viselkedést [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="check-processing-status-for-a-rule"></a>Feldolgozási szabály állapot ellenőrzése

Láthatja, hogy a feldolgozási állapotát és az utolsó frissítés dátuma a tagság a **áttekintése** a csoporthoz tartozó oldal.
  
  ![dinamikus csoport állapotának megjelenítése](./media/groups-create-rule/group-status.png)

A következő állapotüzeneteket is látható, a **tagsági feldolgozási** állapota:

* **Kiértékelése**:  A csoport módosítása érkezett, és a frissítések kiértékelése megtörténik.
* **Feldolgozási**: Frissítés feldolgozása folyamatban van.
* **Frissítés befejezve**: Feldolgozása befejeződött, és minden szükséges frissítés történtek-e.
* **Feldolgozási hiba**:  Feldolgozása sikertelen volt, mert hiba történt a tagsági szabály kiértékelése.
* **Frissítés szüneteltetve**: A dinamikus tagsági szabály frissítések szünetelnek a rendszergazda által. MembershipRuleProcessingState "Felfüggesztett" értékre van állítva.

A következő állapotüzeneteket is látható, a **tagság utolsó frissítés** állapota:

* &lt;**Dátum és idő**&gt;: A legutóbbi alkalommal a tagság frissítése megtörtént.
* **Folyamatban lévő**: Frissítések folyamatban vannak.
* **Ismeretlen**: Nem sikerült beolvasni az utolsó frissítésének időpontját. Lehet, hogy a csoport új.

Az egy adott csoport tagsági szabály feldolgozása során hiba történik, ha egy riasztás tetején látható a **áttekintőlapján** a csoporthoz. Ha nincs függőben van a dinamikus tagsági frissítések feldolgozási az a bérlőn belüli összes csoportra további majd 24 órán keresztül, egy riasztás jelenik meg tetején **összes csoport**.

![a hibajelzések üzenet feldolgozása](./media/groups-create-rule/processing-error.png)

E cikkekben további információk az Azure Active Directory-csoportokon.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Új csoport létrehozása és tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
