---
title: Törölt felhasználók tömeges visszaállítása az Azure Active Directory portálon | Microsoft dokumentumok
description: Törölt felhasználók tömeges visszaállítása az Azure Active Directory Azure AD felügyeleti központjában
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: f75fe224491c2853f819a45db678e87849dc72d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532711"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Törölt felhasználók tömeges visszaállítása az Azure Active Directoryban

Az Azure Active Directory (Azure AD) támogatja a tömeges felhasználói létrehozási és törlési műveleteket, a tömeges meghívást a vendégek számára, és támogatja a felhasználók, csoportok és csoporttagok listájának letöltését.

## <a name="to-bulk-restore-users"></a>Felhasználók tömeges visszaállítása

1. [Jelentkezzen be az Azure AD-szervezetbe](https://aad.portal.azure.com) egy olyan fiókkal, amely az Azure AD-szervezet felhasználói rendszergazdája.
1. Az Azure AD-ben válassza a**Törölt** **felhasználók** > lehetőséget.
1. A **Törölt felhasználók** lapon válassza a **Tömeges visszaállítás** lehetőséget egy érvényes CSV-fájl feltöltéséhez a visszaállítani kívánt felhasználók tulajdonságaiból.

   ![A Tömeges visszaállítás parancs kijelölése a Törölt felhasználók lapon](./media/users-bulk-restore/bulk-restore.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden visszaállítani kívánt felhasználóhoz. Az egyetlen szükséges érték az **ObjectID**. Ezután mentse a fájlt.

   ![Jelöljön ki egy helyi CSV-fájlt, amelyben felsorolja a hozzáadni kívánt felhasználókat](./media/users-bulk-restore/upload-button.png)

1. A **Tömeges visszaállítás** lap **Csv-fájl feltöltése**területén keresse meg a fájlt. Amikor kijelöli a fájlt, és a **Küldés**gombra kattint, elindul a CSV-fájl érvényesítése.
1. A fájl tartalmának ellenőrzése után a **fájl feltöltése sikeresen megjelenik.** Ha vannak hibák, ki kell javítania azokat, mielőtt elküldheti a feladatot.
1. Amikor a fájl megfelel az ellenőrzésen, válassza **a Küldés** lehetőséget az Azure tömeges működését, amely visszaállítja a felhasználókat.
1. Amikor a visszaállítási művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeres volt.

Ha vannak hibák, letöltheti és megtekintheti az eredményfájlt a **Tömeges művelet eredménylapján.** A fájl tartalmazza az egyes hibák okát.

## <a name="check-status"></a>Állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **Tömeges művelet eredménylapján láthatja.**

[![](media/users-bulk-restore/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-restore/bulk-center.png#lightbox)

Ezután ellenőrizheti, hogy a visszaállított felhasználók léteznek-e az Azure AD-szervezetben az Azure Portalon vagy a PowerShell használatával.

## <a name="view-restored-users-in-the-azure-portal"></a>Visszaállított felhasználók megtekintése az Azure Portalon

1. [Jelentkezzen be az Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. A navigációs ablakban válassza az **Azure Active Directory**lehetőséget.
1. A **Kezelés** alatt válassza a **Felhasználókat**.
1. A **Megjelenítés**csoportban válassza a **Minden felhasználó** lehetőséget, és ellenőrizze, hogy a visszaállított felhasználók szerepelnek-e a listában.

### <a name="view-users-with-powershell"></a>Felhasználók megtekintése a PowerShell használatával

Futtassa az alábbi parancsot:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Látnia kell, hogy a visszaállított felhasználók megjelennek a listában.

## <a name="next-steps"></a>További lépések

- [Tömeges importálás felhasználói](users-bulk-add.md)
- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Felhasználók listájának letöltése](users-bulk-download.md)
