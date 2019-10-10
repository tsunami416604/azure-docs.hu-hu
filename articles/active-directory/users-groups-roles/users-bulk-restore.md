---
title: Törölt felhasználók tömeges visszaállítása (előzetes verzió) a Azure Active Directory portálon | Microsoft Docs
description: A törölt felhasználók tömeges visszaállítása az Azure AD felügyeleti központban Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174241"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Törölt felhasználók tömeges visszaállítása (előzetes verzió) Azure Active Directory

Azure Active Directory (Azure AD) támogatja a tömeges felhasználói létrehozási és törlési műveleteket, a csoportos meghívást a vendégek számára, és támogatja a felhasználók, csoportok és csoporttagok letöltési listáját.

## <a name="to-bulk-restore-users"></a>Felhasználók tömeges visszaállítása

1. [Jelentkezzen be az Azure ad-szervezetbe](https://aad.portal.azure.com) egy olyan fiókkal, amely felhasználói rendszergazda az Azure ad-szervezetben.
1. Az Azure AD-ben válassza a **felhasználók** > **törölve**lehetőséget.
1. A **törölt felhasználók** lapon a **tömeges visszaállítás** elemre kattintva töltse fel a visszaállítani kívánt felhasználók tulajdonságaihoz tartozó érvényes CSV-fájlt.

   ![A törölt felhasználók lapon válassza a tömeges visszaállítás parancsot.](./media/users-bulk-restore/bulk-restore.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden visszaállítani kívánt felhasználóhoz. Az egyetlen szükséges érték a **ObjectId**. Ezután mentse a fájlt.

   ![Válassza ki azt a helyi CSV-fájlt, amelyben fel szeretné sorolni a hozzáadni kívánt felhasználókat](./media/users-bulk-restore/upload-button.png)

1. A **tömeges visszaállítás (előzetes verzió)** lapon, a **CSV-fájl feltöltése**területen keresse meg a fájlt. Ha kijelöli a fájlt, majd a **Küldés**gombra kattint, a CSV-fájl érvényesítése elindul.
1. A fájl tartalmának ellenőrzésekor a **fájl feltöltése sikeresen**megtörténik. Ha hibák léptek fel, ezeket a feladatok elküldése előtt ki kell javítania.
1. Ha a fájl érvényesíti az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget az Azure tömeges művelet elindításához, amely visszaállítja a felhasználókat.
1. Ha a visszaállítási művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeresen befejeződött.

Ha hibák léptek fel, letöltheti és megtekintheti az eredményeket tartalmazó fájlt a **tömeges művelet eredményei** lapon. A fájl az egyes hibák okát tartalmazza.

## <a name="check-status"></a>Állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei (előzetes verzió)** lapon tekintheti meg.

   ![Feltöltés állapotának keresése a tömeges műveletek eredményei lapon](./media/users-bulk-restore/bulk-center.png)

Ezután ellenőrizze, hogy a visszaállított felhasználók szerepelnek-e az Azure AD-szervezetben vagy a Azure Portal vagy a PowerShell használatával.

## <a name="view-restored-users-in-the-azure-portal"></a>Visszaállított felhasználók megtekintése a Azure Portal

1. [Jelentkezzen be az Azure ad felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. A navigációs ablaktáblán válassza a **Azure Active Directory**lehetőséget.
1. A **Kezelés** alatt válassza a **Felhasználókat**.
1. A **Megjelenítés**területen válassza a **minden felhasználó** elemet, és ellenőrizze, hogy a visszaállított felhasználók szerepelnek-e a listáján.

### <a name="view-users-with-powershell"></a>Felhasználók megtekintése a PowerShell-lel

Futtassa az alábbi parancsot:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Ekkor megjelenik a visszaállított felhasználók listája.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók tömeges importálása](users-bulk-add.md)
- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Felhasználók listájának letöltése](users-bulk-download.md)
