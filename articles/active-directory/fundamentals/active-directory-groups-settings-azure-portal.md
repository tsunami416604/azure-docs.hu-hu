---
title: Az Azure Active Directory csoport adatainak szerkesztése |} A Microsoft Docs
description: Hogyan lehet módosítani egy csoport adatait az Azure Active Directoryval.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 0bac740a5da9c1e57ea1755c58579463da4a883a
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580438"
---
# <a name="how-to-edit-your-group-information-using-azure-active-directory"></a>Útmutató: az Azure Active Directory használatával csoport adatainak szerkesztése

Az Azure Active Directoryval, szerkesztheti a csoport beállításait, többek között a neve, leírása és tagságtípusának frissítése.

## <a name="to-edit-your-group-settings"></a>A csoport beállításainak módosítása
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) a címtár egy globális rendszergazdai fiók használatával.

2. Válassza ki **Azure Active Directory**, majd válassza ki **csoportok**.

    A **csoportok – összes csoport** megjelenítő lap jelenik meg, az összes aktív csoportot.

3. Az a **csoportok – összes csoport** mint az a csoport nevét írja be a **keresési** mezőbe. Ez a cikk céljából, hogy keres a **mobileszköz-kezelési szabályzat – Nyugat-India** csoport.

    A keresési eredmények között meg fog jelenni a **keresési** frissítése több karakter beírása mezőben.

    ![Az összes csoport lapon, a keresési szöveget a keresőmezőbe](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Válassza ki azt a csoportot **mobileszköz-kezelési szabályzat – Nyugat-India**, majd válassza ki **tulajdonságok** a a **kezelés** területen.

    ![Csoport számát és a tagok és tag opció kiemelésével – áttekintés oldalra](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Frissítés a **általános beállítások** információkat, ha szükséges, beleértve:

    ![Egy csoport beállításainak tulajdonságai](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Csoport neve.** Szerkessze a meglévő csoportnevet.
    
    - **A csoport leírása.** Szerkessze a meglévő csoport leírását.

    - **Csoport típusa.** Miután létrejött a csoport típusa nem módosítható. Módosíthatja a **csoporttípust**, akkor törölje a csoportot, majd hozzon létre egy új.
    
    - **Tagság típusa.** A tagsági típus módosítása A rendelkezésre álló tagságát különféle típusaival kapcsolatos további információkért lásd: [hogyan: hozzon létre egy alapszintű csoportot és felhasználókat adhatnának használata az Azure Active Directory portálon](active-directory-groups-create-azure-portal.md)
    
    - **Objektum azonosítója.** Az Objektumazonosító nem módosítható, de az, hogy a PowerShell-parancsokban a csoport számára is másolhatja. PowerShell-parancsmagok használatával kapcsolatos további információkért lásd: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

- [A csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [Hozzon létre egy alapszintű csoportot, és tagokat vehet fel](active-directory-groups-create-azure-portal.md)

- [Hogyan lehet hozzáadni, vagy a tagok eltávolítása egy csoportból](active-directory-groups-members-azure-portal.md)

- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../users-groups-roles/groups-create-rule.md)

- [Csoporttagságok kezelése](active-directory-groups-membership-azure-portal.md)

- [Csoportok használata erőforrásaihoz való hozzáférés kezelése](active-directory-manage-groups.md)

- [Hozzárendelése vagy Azure-előfizetés hozzáadása az Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)
