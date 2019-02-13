---
title: Szerkessze a csoport adatait – Azure Active Directory |} A Microsoft Docs
description: Szerkessze a csoport adatait az Azure Active Directoryval kapcsolatos utasításokat.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5802634fe070f5d71fae01706c3cb4e83e04a72e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56192064"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>A csoport adatait az Azure Active Directoryval

Azure Active Directory (Azure AD) használatával, módosíthatja egy csoport beállításait, többek között a nevét, leírását és tagsági típusa frissítése.

## <a name="to-edit-your-group-settings"></a>A csoport beállításainak módosítása
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

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
    
    - **A csoport ismertetése.** Szerkessze a meglévő csoport leírását.

    - **Csoport típusa.** Miután létrejött a csoport típusa nem módosítható. Módosíthatja a **csoporttípust**, akkor törölje a csoportot, majd hozzon létre egy új.
    
    - **Tagság típusa.** A tagsági típus módosítása A rendelkezésre álló tagságát különféle típusaival kapcsolatos további információkért lásd: [hogyan: Hozzon létre egy alapszintű csoportot és felhasználókat adhatnának használata az Azure Active Directory portálon](active-directory-groups-create-azure-portal.md).
    
    - **Objektum azonosítója.** Az Objektumazonosító nem módosítható, de az, hogy a PowerShell-parancsokban a csoport számára is másolhatja. PowerShell-parancsmagok használatával kapcsolatos további információkért lásd: [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

- [Csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [Hozzon létre egy alapszintű csoportot, és tagokat vehet fel](active-directory-groups-create-azure-portal.md)

- [Hogyan lehet hozzáadni, vagy a tagok eltávolítása egy csoportból](active-directory-groups-members-azure-portal.md)

- [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../users-groups-roles/groups-create-rule.md)

- [Csoporttagságok kezelése](active-directory-groups-membership-azure-portal.md)

- [Az erőforrásokhoz való hozzáférés kezelése csoportokkal](active-directory-manage-groups.md)

- [Azure-előfizetés társítása vagy hozzáadása az Azure Active Directoryhoz](active-directory-how-subscriptions-associated-directory.md)
