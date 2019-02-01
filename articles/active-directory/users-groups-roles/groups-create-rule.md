---
title: Dinamikus csoport létrehozása és állapotának – Azure Active Directory |} A Microsoft Docs
description: Az Azure portal-szabályok létrehozása egy csoport tagságát, állapotának ellenőrzése.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 7e283127fa9effe04f59f31c836f471020c9dacc
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55506942"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Dinamikus csoport létrehozása és állapotának ellenőrzése

Az Azure Active Directoryban (Azure AD) létrehozhat csoportokat úgy, hogy alkalmazza a szabályt, amely meghatározza a tagsági felhasználó vagy az eszköz tulajdonságok alapján. Amikor egy felhasználó vagy eszköz módosítások attribútumait, az Azure AD kiértékeli az összes dinamikus csoport-szabályok az Azure AD-bérlő, és bármely hajt végre hozzáadása vagy eltávolítása. Ha egy felhasználó vagy az eszköz megfelel a egy szabályt a csoporthoz, azok lesz hozzáadva tagként, és azokat a szabály már nem felel meg, amikor eltávolítja.

Ez a cikk részletesen bemutatja az Azure Portalon a biztonsági vagy Office 365-csoportokat a dinamikus tagsági szabály beállításához. A szabály szintaxisra vonatkozó példákat és a támogatott tulajdonságok, operátorok és a tagsági szabály értékeinek teljes listáját lásd: [dinamikus tagsági szabályok az Azure Active Directoryban csoportok](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>A csoporttagsági szabály létrehozása

1. Jelentkezzen be a [Azure AD felügyeleti központ](https://aad.portal.azure.com) egy olyan fiókkal, amely a bérlő globális rendszergazdai, az Intune szolgáltatás-rendszergazda vagy a felhasználóifiók-adminisztrátor szerepkör.
2. Válassza ki **csoportok**.
3. Válassza ki **összes csoport**, és válassza ki **új csoport**.

   ![Új csoport hozzáadása](./media/groups-create-rule/new-group-creation.png)

4. Az a **csoport** panelen adjon meg egy nevet és leírást az új csoport. Válassza ki a **tagsági típussal** lehet **dinamikus felhasználói** vagy **dinamikus eszköz**, attól függően, hogy hozzon létre egy szabályt, felhasználók vagy eszközök számára, és válassza ki a szeretné**Dinamikus lekérdezés hozzáadása**. A szabály builder használatával egy egyszerű szabályt hozhat létre, vagy saját maga a tagsági szabály írása. Ez a cikk tartalmaz további információt az elérhető felhasználó és eszköz attribútumait, valamint a tagsági szabályok példái.

   ![Dinamikus tagsági szabály hozzáadása](./media/groups-create-rule/add-dynamic-group-rule.png)

5. A tagsági lekérdezést adhat hozzá egyéni bővítménytulajdonság teljes listájának megtekintéséhez, válassza ki a **egyéni bővítménytulajdonság lekérése**, adja meg az Alkalmazásazonosítót, és válassza **tulajdonságainak frissítése**. A teljes Izraeli normál idő szerint tulajdonságok kiválasztható lesz.
6. A szabály létrehozása után válassza ki a **lekérdezés hozzáadása** a panel alján.
7. Válassza ki **létrehozás** a a **csoport** panelen a csoport létrehozásához.

> [!TIP]
> Csoport létrehozása sikertelen lesz, ha a megadott szabály helytelenül formázott vagy érvénytelen volt. A tartalmazó annak magyarázatát, miért érdemes a szabályt nem sikerült feldolgozni a portál jobb felső sarkában megjelenik egy értesítés. Olvassa el, hogy alaposan megismerheti, hogyan, hogy a szabály érvényes, hogy módosítani kell.

## <a name="check-processing-status-for-a-membership-rule"></a>Ellenőrizze a tagsági szabály feldolgozási állapota

Láthatja, hogy a feldolgozási állapotát és az utolsó frissítés dátuma a tagság a **áttekintése** a csoporthoz tartozó oldal.
  
  ![dinamikus csoport állapotának megjelenítése](./media/groups-create-rule/group-status.png)

A következő állapotüzeneteket is látható, a **tagsági feldolgozási** állapota:

* **Kiértékelése**:  A csoport módosítása érkezett, és a frissítések kiértékelése megtörténik.
* **Feldolgozási**: Frissítés feldolgozása folyamatban van.
* **Frissítés befejezve**: Feldolgozása befejeződött, és minden szükséges frissítés történtek-e.
* **Feldolgozási hiba**: Hiba történt a tagsági szabály értékelése és feldolgozása nem fejeződött be.
* **Frissítés szüneteltetve**: A dinamikus tagsági szabály frissítések szünetelnek a rendszergazda által. MembershipRuleProcessingState "Felfüggesztett" értékre van állítva.

A következő állapotüzeneteket is látható, a **tagság utolsó frissítés** állapota:

* &lt;**Dátum és idő**&gt;: A legutóbbi alkalommal a tagság frissítése megtörtént.
* **Folyamatban lévő**: Frissítések folyamatban vannak.
* **Ismeretlen**: Nem sikerült beolvasni az utolsó frissítésének időpontját. Ennek oka az újonnan létrehozott csoport lehet.

Az egy adott csoport tagsági szabály feldolgozása során hiba történik, ha egy riasztás tetején látható a **áttekintőlapján** a csoporthoz. Ha nincs függőben van a dinamikus tagsági frissítések feldolgozási az a bérlőn belüli összes csoportra további majd 24 órán keresztül, egy riasztás jelenik meg tetején **összes csoport**.

![Hiba történt az üzenet feldolgozásakor](./media/groups-create-rule/processing-error.png)

E cikkekben további információk az Azure Active Directory-csoportokon.

* [Meglévő csoportok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Új csoport létrehozása és tagok hozzáadása](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Csoportbeállítások kezelése](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Csoporttagságok kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-dynamic-membership.md)
