---
title: A csoportadatok szerkesztése – Azure Active Directory | Microsoft dokumentumok
description: Útmutató a csoport adatainak az Azure Active Directory használatával történő szerkesztéséhez.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561884"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>A csoportadatok szerkesztése az Azure Active Directory használatával

Az Azure Active Directory (Azure AD) használatával szerkesztheti a csoport beállításait, beleértve a név, leírás vagy tagságtípus frissítését.

## <a name="to-edit-your-group-settings"></a>A csoportbeállítások szerkesztése
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza az **Azure Active Directory**lehetőséget, majd a **Csoportok**lehetőséget.

    Megjelenik **a Csoportok – Minden csoport** lap, amely az összes aktív csoportot jeleníti meg.

3. A **Csoportok – Minden csoport** lapon írja be a csoport nevének mekkora részét a **Keresőmezőbe.** Ennek a cikknek az alkalmazásában az **MDM-házirend - Nyugati** csoport keresése.

    A keresési eredmények a **Keresés** mezőben jelennek meg, és a további karakterek beírásakor frissülnek.

    ![Minden csoport lap, a keresőszöveggel a Keresőmezőben](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Válassza ki a csoport **MDM-házirend - Nyugat**, majd válassza **tulajdonságok** a **Kezelés** területen.

    ![Csoport áttekintése lap, a Tag lehetőséggel és a kiemelt információkkal](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Szükség szerint frissítse az **általános beállításokra** vonatkozó információkat, többek között:

    ![Csoport tulajdonságbeállításai](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Csoport neve.** A meglévő csoportnév szerkesztése.
    
    - **A csoport ismertetése.** Szerkessze a meglévő csoportleírást.

    - **Csoport típusa.** A csoport típusa nem módosítható a létrehozás után. A **Csoport típusának**módosításához törölnie kell a csoportot, és újat kell létrehoznia.
    
    - **Tagság típusa.** Módosítsa a tagság típusát. A különböző elérhető tagsági típusokról további információt a [Hogyan hozhat létre: Egyszerű csoport létrehozása és tagok hozzáadása az Azure Active Directory portálon.](active-directory-groups-create-azure-portal.md)
    
    - **Objektumazonosító.** Az objektumazonosító nem módosítható, de másolhatja a csoport PowerShell-parancsaiban való használatra. A PowerShell-parancsmagok használatáról további információt az [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásáról](../users-groups-roles/groups-settings-v2-cmdlets.md)című témakörben talál.

## <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

- [Csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- [Tagok hozzáadása és eltávolítása a csoportból](active-directory-groups-members-azure-portal.md)

- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../users-groups-roles/groups-create-rule.md)

- [Csoporttagságok kezelése](active-directory-groups-membership-azure-portal.md)

- [Az erőforrásokhoz való hozzáférés kezelése csoportokkal](active-directory-manage-groups.md)

- [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)
