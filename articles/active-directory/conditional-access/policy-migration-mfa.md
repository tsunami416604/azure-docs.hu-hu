---
title: Feltételes hozzáférési házirendek áttelepítése – Azure Active Directory
description: Ez a cikk bemutatja, hogyan telepítheti át a klasszikus szabályzatot, amely többtényezős hitelesítést igényel az Azure Portalon.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d637ac464b689a25ce5d5a79cf47da0c85d38d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846026"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Klasszikus szabályzat áttelepítése az Azure Portalon

Ez a cikk bemutatja, hogyan lehet áttelepíteni egy klasszikus szabályzatot, amely **többtényezős hitelesítést** igényel egy felhőalapú alkalmazáshoz. Bár ez nem előfeltétel, azt javasoljuk, hogy olvassa el [a klasszikus szabályzatok áttelepítése az Azure Portalon,](policy-migration.md) mielőtt elkezdené a klasszikus szabályzatok áttelepítése.

![Klasszikus házirend-részletek, amelyek a Salesforce alkalmazáshoz többfa-t igényelnek](./media/policy-migration/33.png)

Az áttelepítési folyamat a következő lépésekből áll:

1. [Nyissa meg a klasszikus házirendet](#open-a-classic-policy) a konfigurációs beállítások leéséhez.
1. Hozzon létre egy új Azure AD feltételes hozzáférési szabályzatot a klasszikus szabályzat lecseréléséhez. 
1. Tiltsa le a klasszikus házirendet.

## <a name="open-a-classic-policy"></a>Klasszikus házirend megnyitása

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Válassza a **Klasszikus házirendek**lehetőséget.

   ![Klasszikus házirendek nézet](./media/policy-migration-mfa/12.png)

1. A klasszikus házirendek listájában válassza ki az áttelepíteni kívánt házirendet. Dokumentálja a konfigurációs beállításokat, hogy újra létrehozhassa az új feltételes hozzáférési házirendet.

## <a name="create-a-new-conditional-access-policy"></a>Új feltételes hozzáférési házirend létrehozása

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az **Azure Active Directory** > **biztonsági** > **feltételes hozzáférését.**
1. Új feltételes hozzáférési házirend létrehozásához válassza az **Új házirend**lehetőséget.
1. Az **Új** lap **Név** mezőjébe írja be a házirend nevét.
1. A **Hozzárendelések csoportban** kattintson a **Felhasználók és csoportok**elemre.
   1. Ha a klasszikus házirendben az összes felhasználó tikket, kattintson a **Minden felhasználó gombra.** 
   1. Ha a klasszikus házirendben kijelölt csoportok vannak kijelölve, kattintson a **Felhasználók és csoportok kijelölése**gombra, majd válassza ki a szükséges felhasználókat és csoportokat.
   1. Ha rendelkezik a kizárt csoportokkal, kattintson a **Kizárás** fülre, majd válassza ki a szükséges felhasználókat és csoportokat. 
   1. Válassza a **Kész** lehetőséget
1. A **Hozzárendelés** csoportban kattintson a **Felhőalapú alkalmazások vagy műveletek**elemre.
1. A **Felhőalkalmazások vagy műveletek** lapon hajtsa végre az alábbi lépéseket:
   1. Kattintson **az Alkalmazások kiválasztása gombra.**
   1. Kattintson a **Kiválasztás** gombra.
   1. A **Kijelölés** lapon jelölje ki a felhőalapú alkalmazást, majd kattintson a **Kijelölés gombra.**
   1. A **Felhőalapú alkalmazások** lapon kattintson a **Kész**gombra.
1. Ha a **Többtényezős hitelesítés megkövetelése** jelölőnégyzet be van jelölve:
   1. A **Hozzáférés vezérlői** csoportban kattintson a **Támogatás**gombra.
   1. A **Grant** lapon kattintson a **Hozzáférés megadása**gombra, majd a **Többtényezős hitelesítés megkövetelése**parancsra.
   1. Kattintson a **Kiválasztás** gombra.
1. Kattintson **a Be gombra** a házirend engedélyezéséhez, majd válassza a **Mentés lehetőséget.**

   ![Feltételes hozzáférési házirend létrehozása](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>A klasszikus házirend letiltása

A klasszikus házirend letiltásához kattintson a **Letiltás gombra** a **Részletek** nézetben.

![Klasszikus házirendek letiltása](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>További lépések

- A klasszikus szabályzatok áttelepítéséről a [Klasszikus szabályzatok áttelepítése az Azure Portalon](policy-migration.md)című témakörben talál további információt.
- [Az új házirend-döntések hatásának meghatározásához használja a csak jelentésmódot a feltételes hozzáféréshez.](concept-conditional-access-report-only.md)