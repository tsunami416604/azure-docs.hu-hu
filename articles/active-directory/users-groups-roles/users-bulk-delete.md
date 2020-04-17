---
title: Felhasználók tömeges törlése az Azure Active Directory portálon | Microsoft dokumentumok
description: Felhasználók törlése tömegesen az Azure Active Directory Azure Felügyeleti központjában
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
ms.openlocfilehash: beb8b4f35dc5f02e59cced05a6bcfc235d42f996
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532813"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Felhasználók tömeges törlése az Azure Active Directoryban

Az Azure Active Directory (Azure AD) portál használatával eltávolíthatja a tagok nagy számú egy csoport segítségével egy vesszővel tagolt értékek (CSV) fájl tömeges felhasználók tömeges törlése.

## <a name="to-bulk-delete-users"></a>Felhasználók tömeges törlése

1. [Jelentkezzen be az Azure AD-szervezetbe](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. Az Azure AD-ben válassza a **Felhasználók** > **tömeges törlése**lehetőséget.
1. A **Tömeges törlés felhasználói** lapon válassza a **Letöltés** lehetőséget a felhasználói tulajdonságok érvényes CSV-fájljának fogadásához.

   ![Jelöljön ki egy helyi CSV-fájlt, amelyben felsorolja a törölni kívánt felhasználókat](./media/users-bulk-delete/bulk-delete.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden törölni kívánt felhasználóhoz. Az egyetlen szükséges érték az **egyszerű felhasználó neve**. Ezután mentse a fájlt.

   ![A CSV-fájl tartalmazza a törölni kívánt felhasználók neveit és azonosítóit](./media/users-bulk-delete/delete-csv-file.png)

1. A **Tömeges törlés felhasználói** lap **Csv-fájl feltöltése**területén keresse meg a fájlt. Amikor kijelöli a fájlt, és a Küldés gombra kattint, elindul a CSV-fájl érvényesítése.
1. A fájl tartalmának ellenőrzése után a **fájl feltöltése sikeresen megjelenik.** Ha vannak hibák, ki kell javítania azokat, mielőtt elküldheti a feladatot.
1. Amikor a fájl megfelel az ellenőrzésen, válassza **a Küldés** lehetőséget az Azure tömeges műveletének elindításához, amely törli a felhasználókat.
1. Amikor a törlési művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeres volt.

Ha vannak hibák, letöltheti és megtekintheti az eredményfájlt a **Tömeges művelet eredménylapján.** A fájl tartalmazza az egyes hibák okát.

## <a name="check-status"></a>Állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **Tömeges művelet eredménylapján láthatja.**

   [![](media/users-bulk-delete/bulk-center.png "Check delete status in the Bulk Operations Results page")](media/users-bulk-delete/bulk-center.png#lightbox)

Ezután ellenőrizheti, hogy a törölt felhasználók léteznek-e az Azure AD-szervezetben az Azure Portalon vagy a PowerShell használatával.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Törölt felhasználók ellenőrzése az Azure Portalon

1. Jelentkezzen be az Azure Portalra egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. A navigációs ablakban válassza az **Azure Active Directory**lehetőséget.
1. A **Kezelés** alatt válassza a **Felhasználókat**.
1. A **Megjelenítés**csoportban jelölje be a **Csak felhasználók** jelölőnégyzetet, és ellenőrizze, hogy a törölt felhasználók már nem szerepelnek-e a listában.

### <a name="verify-deleted-users-with-powershell"></a>Törölt felhasználók ellenőrzése a PowerShell használatával

Futtassa az alábbi parancsot:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Ellenőrizze, hogy a törölt felhasználók már nem szerepelnek-e a listában.

## <a name="next-steps"></a>További lépések

- [Felhasználók tömeges hozzáadása](users-bulk-add.md)
- [Felhasználók listájának letöltése](users-bulk-download.md)
- [Tömeges visszaállítási felhasználók](users-bulk-restore.md)
