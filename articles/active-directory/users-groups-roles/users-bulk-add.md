---
title: Felhasználók tömeges létrehozása az Azure Active Directory portálon | Microsoft dokumentumok
description: Felhasználók tömeges hozzáadása az Azure Active Directory Azure AD felügyeleti központjában
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3a8b9cb9701288d24534ab08940f6dbd4a698ad
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532932"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Felhasználók tömeges létrehozása az Azure Active Directoryban

Az Azure Active Directory (Azure AD) támogatja a tömeges felhasználói létrehozási és törlési műveleteket, a tömeges meghívást a vendégek számára, és támogatja a felhasználók, csoportok és csoporttagok listájának letöltését.

## <a name="required-permissions"></a>Szükséges engedélyek

Ahhoz, hogy tömegesen hozhasson létre felhasználókat a felügyeleti portálon, globális rendszergazdaként vagy felhasználóként kell bejelentkeznie.

## <a name="to-create-users-in-bulk"></a>Felhasználók tömeges létrehozása

1. [Jelentkezzen be az Azure AD-szervezetbe](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. Az Azure AD-ben válassza a **Felhasználók** > **tömeges létrehozása lehetőséget.**
1. A **Tömeges létrehozás felhasználói** lapon válassza a **Letöltés** lehetőséget, ha a felhasználói tulajdonságokat egy érvényes, vesszővel tagolt értékfájl (CSV) kapja meg, majd adja hozzá a létrehozni kívánt felhasználókat.

   ![Jelöljön ki egy helyi CSV-fájlt, amelyben felsorolja a hozzáadni kívánt felhasználókat](./media/users-bulk-add/upload-button.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden létrehozni kívánt felhasználóhoz. Az egyetlen szükséges érték a **Név**, **az egyszerű felhasználónév**, **a kezdeti jelszó** és a **Bejelentkezés blokkolása (Igen/Nem).** Ezután mentse a fájlt.

   ![A CSV-fájl tartalmazza a létrehozandó felhasználók neveit és azonosítóit](./media/users-bulk-add/add-csv-file.png)

1. A **Tömeges létrehozás felhasználói** lapon a CSV-fájl feltöltése csoportban keresse meg a fájlt. Amikor kijelöli a fájlt, és a **Küldés**gombra kattint, elindul a CSV-fájl érvényesítése.
1. A fájl tartalmának ellenőrzése után a **fájl feltöltése sikeresen megjelenik.** Ha vannak hibák, ki kell javítania azokat, mielőtt elküldheti a feladatot.
1. Amikor a fájl megfelel az ellenőrzésen, válassza **a Küldés** lehetőséget az új felhasználókat importáló Azure tömeges művelet elindításához.
1. Amikor az importálási művelet befejeződik, megjelenik egy értesítés a tömeges műveleti feladat állapotáról.

Ha vannak hibák, letöltheti és megtekintheti az eredményfájlt a **Tömeges művelet eredménylapján.** A fájl tartalmazza az egyes hibák okát.

## <a name="check-status"></a>Állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **Tömeges művelet eredménylapján láthatja.**

   [![](media/users-bulk-add/bulk-center.png "Check create status in the Bulk Operations Results page")](media/users-bulk-add/bulk-center.png#lightbox)

Ezután ellenőrizheti, hogy a létrehozott felhasználók léteznek-e az Azure AD-szervezetben az Azure Portalon vagy a PowerShell használatával.

## <a name="verify-users-in-the-azure-portal"></a>Felhasználók ellenőrzése az Azure Portalon

1. [Jelentkezzen be az Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. A navigációs ablakban válassza az **Azure Active Directory**lehetőséget.
1. A **Kezelés** alatt válassza a **Felhasználókat**.
1. A **Megjelenítés**csoportban válassza a **Minden felhasználó** lehetőséget, és ellenőrizze, hogy a létrehozott felhasználók szerepelnek-e a listában.

### <a name="verify-users-with-powershell"></a>Felhasználók ellenőrzése a PowerShell használatával

Futtassa az alábbi parancsot:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Látnia kell, hogy a létrehozott felhasználók szerepelnek a listában.

## <a name="bulk-import-service-limits"></a>Tömeges importálási szolgáltatási korlátok

A felhasználók létrehozásához a felhasználók létrehozásához végzett minden egyes tömeges tevékenység legfeljebb egy órán keresztül futhat. Ez legalább 50 000 felhasználó tömeges létrehozását teszi lehetővé.

## <a name="next-steps"></a>További lépések

- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Felhasználók listájának letöltése](users-bulk-download.md)
- [Tömeges visszaállítási felhasználók](users-bulk-restore.md)
