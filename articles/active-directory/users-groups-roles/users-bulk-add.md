---
title: Felhasználók tömeges létrehozása (előzetes verzió) a Azure Active Directory portálon | Microsoft Docs
description: Felhasználók tömeges hozzáadása az Azure AD felügyeleti központban Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174318"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Felhasználók tömeges létrehozása (előzetes verzió) Azure Active Directory

Azure Active Directory (Azure AD) támogatja a tömeges felhasználói létrehozási és törlési műveleteket, a csoportos meghívást a vendégek számára, és támogatja a felhasználók, csoportok és csoporttagok letöltési listáját.

## <a name="required-permissions"></a>Szükséges engedélyek

A felhasználók tömeges létrehozásához a felügyeleti portálon globális rendszergazdaként vagy felhasználói rendszergazdaként kell bejelentkeznie.

## <a name="to-create-users-in-bulk"></a>Felhasználók tömeges létrehozása

1. [Jelentkezzen be az Azure ad-szervezetbe](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. Az Azure AD-ben válassza a **felhasználók** > **tömeges létrehozás**elemet.
1. A felhasználó **tömeges létrehozása** lapon válassza a **Letöltés** lehetőséget a felhasználói tulajdonságok érvényes, VESSZŐVEL tagolt (CSV) fájljának megjelenítéséhez, majd adja hozzá a létrehozni kívánt felhasználókat.

   ![Válassza ki azt a helyi CSV-fájlt, amelyben fel szeretné sorolni a hozzáadni kívánt felhasználókat](./media/users-bulk-add/upload-button.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden létrehozni kívánt felhasználóhoz. Az egyetlen szükséges érték a **név**, a **egyszerű felhasználónév**, a **kezdeti jelszó** és a **blokkolási bejelentkezés (igen/nem)** . Ezután mentse a fájlt.

   ![A CSV-fájl a létrehozandó felhasználók nevét és azonosítóit tartalmazza](./media/users-bulk-add/add-csv-file.png)

1. A **felhasználók tömeges létrehozása (előzetes verzió)** lapon, a CSV-fájl feltöltése területen keresse meg a fájlt. Ha kijelöli a fájlt, majd a **Küldés**gombra kattint, a CSV-fájl érvényesítése elindul.
1. A fájl tartalmának ellenőrzése után a **fájl feltöltése sikeresen**megtörténik. Ha hibák léptek fel, ezeket a feladatok elküldése előtt ki kell javítania.
1. Amikor a fájl átadja az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget, hogy elindítsa az új felhasználókat importáló Azure bulk műveletet.
1. Amikor az importálási művelet befejeződik, megjelenik egy értesítés a tömeges művelet állapotáról.

Ha hibák léptek fel, letöltheti és megtekintheti az eredményeket tartalmazó fájlt a **tömeges művelet eredményei** lapon. A fájl az egyes hibák okát tartalmazza.

## <a name="check-status"></a>Állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei (előzetes verzió)** lapon tekintheti meg.

   ![Feltöltés állapotának keresése a tömeges műveletek eredményei lapon](./media/users-bulk-add/bulk-center.png)

Ezután ellenőrizze, hogy a létrehozott felhasználók szerepelnek-e az Azure AD-szervezetben vagy a Azure Portal vagy a PowerShell használatával.

## <a name="verify-users-in-the-azure-portal"></a>Felhasználók ellenőrzése a Azure Portalban

1. [Jelentkezzen be az Azure ad felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
1. A navigációs ablaktáblán válassza a **Azure Active Directory**lehetőséget.
1. A **Kezelés** alatt válassza a **Felhasználókat**.
1. A **Megjelenítés**területen válassza a **minden felhasználó** lehetőséget, és győződjön meg arról, hogy a létrehozott felhasználók szerepelnek a felsorolásban.

### <a name="verify-users-with-powershell"></a>Felhasználók ellenőrzése a PowerShell-lel

Futtassa az alábbi parancsot:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Látnia kell, hogy a létrehozott felhasználók szerepelnek a listáján.

## <a name="bulk-import-service-limits"></a>Tömeges importálási szolgáltatás korlátai

A felhasználók létrehozásához minden tömeges tevékenység akár egy óráig is futhat. Ez legalább 50 000 felhasználó tömeges létrehozását teszi lehetővé.

## <a name="next-steps"></a>Következő lépések

- [Felhasználók tömeges törlése](users-bulk-delete.md)
- [Felhasználók listájának letöltése](users-bulk-download.md)
- [Felhasználók tömeges visszaállítása](users-bulk-restore.md)
