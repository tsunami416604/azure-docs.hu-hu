---
title: Tagok és tulajdonosok keresése és szűrése (előzetes verzió) – Azure Active Directory | Microsoft dokumentumok
description: A keresés és a szűrés csoportosítja a tagokat és a tulajdonosokat az Azure Portalon.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/28/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a815446b79b3e5ec0a75e5d179953956643b16c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206112"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Csoportok és tagok keresése (előzetes verzió) az Azure Active Directoryban

Ez a cikk bemutatja, hogyan kereshet tagokat és tulajdonosokat egy csoport, és hogyan használhatja a keresési szűrők et az Azure Active Directory (Azure AD) portálon a csoportok fejlesztési előnézetének részeként. A csoportok számos fejlesztést tartalmaznak, amelyek segítenek a csoportok gyors és egyszerű kezelésében, beleértve a tagokat és a tulajdonosokat is. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az előnézet változásai a következők:

- Új csoportok keresési lehetőségei, például karakterlánc-részkeresés a csoportnevekben
- Új szűrési és rendezési beállítások a tag- és tulajdonoslistákon
- Új keresési lehetőségek a tagok és a tulajdonosok listáihoz
- Pontosabb csoportszámok nagy csoportok esetén

## <a name="enabling-and-managing-the-preview"></a>Az előnézet engedélyezése és kezelése

Megkönnyítettük az előzetes verzióhoz való csatlakozást:

  1. Jelentkezzen be az [Azure AD portálra](https://portal.azure.com), és válassza a **Csoportok**lehetőséget.
  2. A Csoportok – Minden csoport lapon jelölje ki a szalagcímet a lap tetején, hogy csatlakozzon az előnézethez.

A legújabb funkciókat és fejlesztéseket úgy is megtekintheti, hogy a Minden csoport lapon **az Előnézeti adatok** lehetőséget **választja.** Miután csatlakozott az előnézethez, megtekintheti az előnézeti címkét az összes olyan csoportoldalon, amelynek fejlesztése van, és amely az előnézet részét képezi. Nem minden csoportlap lett frissítve az előnézet részeként.

Ha bármilyen problémája van, visszaválthat az örökölt élményre, ha kiválasztja a szalagcímet a **Minden csoport** lap tetején. Nagyra értékeljük a visszajelzést, hogy javíthassuk tapasztalatunkat.

## <a name="group-search-and-sorting"></a>Csoportos keresés és rendezés

A csoportlista keresése tovább lett fejlesztve, így amikor beírhat `startswith` egy keresési karakterláncot, a keresés automatikusan végrehajtegy és karakterlánc-részkeresést a csoportnevek listáján. A karakterlánc-részkeresés csak teljes szavakon történik, és nem tartalmaz speciális karaktereket. A karakterláncrész-keresés ben a kis- és nagybetűk et nem lehet figyelembe.

![új részkarakterlánc-keresések a Minden csoport lapon](./media/groups-members-owners-search/groups-search-preview.png)

A "házirend" kifejezésre való keresés például most antól az "MDM-házirend – Nyugat" és a "Házirendcsoport" szöveget is visszaadja. A "New_policy" nevű csoportot nem adja vissza.

- Ugyanezt a keresést a csoporttagsági listákon is elvégezheti.
- A lista mostantól név szerint rendezhető a névoszlop fejlécéből jobbra lévő nyilakkal a lista növekvő vagy csökkenő sorrendben történő rendezéséhez.

## <a name="group-member-search-and-filtering"></a>Csoporttag keresése és szűrése

### <a name="search-group-member-and-owner-lists"></a>A csoport tagjainak és tulajdonosi listáinak keresése

Most antól név szerint kereshet egy adott csoport tagjai között, és ugyanezt a keresést a csoport tulajdonosainak listáján is elvégezheti. Az új felületen, ha karakterláncot ad meg a keresőmezőbe, a kezdetű keresés automatikusan elvégezhető. A "Scott" kifejezésre keresve például Scott Wilkinson lesz a visszaadás.

![új részkarakterlánc-keresések a csoporttagok és a tulajdonosok listáján](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Tagok és tulajdonosok listájának szűrése

A keresés mellett mostantól a tag- és tulajdonoslistákat is szűrheti felhasználótípus szerint. Ez a lista Felhasználó típus oszlopában található információ. Így szűrheti a listát a tagok és a vendégek, hogy meghatározza, ha vannak olyan vendégek a csoportban.

### <a name="view-and-manage-membership"></a>Tagság megtekintése és kezelése

Az adott csoport közvetlen tagjainak megtekintése mellett a csoport összes tagjának listáját is megtekintheti a Tagok oldalon. A tagok listája tartalmazza a csoport összes egyedi tagját, beleértve a tranzitív tagokat is.

A közvetlen tagok és az összes tag egyenként is kereshetésés szűrése. Az összes taglista szűrése nincs hatással a közvetlen tagok listájára alkalmazott szűrőkre.

## <a name="improved-group-member-counts"></a>Továbbfejlesztett csoporttagok száma

Javítottuk a csoport **áttekintése** oldalt, hogy a csoporttagok számát minden méretű csoporthoz megadhassuk. A tagok száma még az 1000-nél több tagot számláló csoportok esetében is látható. Most már láthatja a csoport közvetlen tagjainak teljes számát és a teljes tagsági számot (a csoport összes egyedi tagját, beleértve a tranzitív tagokat is) az **Áttekintés** oldalon.

![Nagyobb pontosság a csoporttagságok számában](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>További lépések

Ezek a cikkek további információt nyújtanak a csoportok azure AD-ben való munkáról.

- [Csoportok és tagok megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Csoporttagság kezelése](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](groups-create-rule.md)
- [A csoport beállításainak szerkesztése](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Az erőforrásokhoz való hozzáférés kezelése csoportokkal](../fundamentals/active-directory-manage-groups.md)
- [Az SaaS-alkalmazásokhoz való hozzáférés kezelése csoportokkal](groups-saasapps.md)
- [Csoportok kezelése PowerShell-parancsokkal](groups-settings-v2-cmdlets.md)
- [Azure-előfizetés hozzáadása az Azure Active Directoryhoz](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
