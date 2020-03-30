---
title: Tömeges importálási feltöltés tagok csoporthoz való hozzáadásához - Azure Active Directory | Microsoft dokumentumok
description: Csoporttagok tömeges hozzáadása az Azure Active Directory felügyeleti központban.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ff4234d9fd28e655e868108e37b09c5afe2767c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517126"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Tömeges importálási csoporttagok (előzetes verzió) az Azure Active Directoryban

Az Azure Active Directory (Azure AD) portál használatával nagyszámú tagot adhat hozzá egy csoporthoz egy vesszővel tagolt értékfájl (CSV) használatával a csoporttagok tömeges importálásához.

## <a name="to-bulk-import-group-members"></a>Csoporttagok tömeges importálása

1. Jelentkezzen be [az Azure Portalon](https://portal.azure.com) egy felhasználói rendszergazdai fiókkal a szervezetben. A csoporttulajdonosok tömegesen is importálhatnak a saját csoporttagjaikat.
1. Az Azure AD-ben válassza **a Csoportok** > **minden csoport lehetőséget.**
1. Nyissa meg azt a csoportot, amelyhez tagokat ad hozzá, majd válassza a **Tagok**lehetőséget.
1. A **Tagok** lapon válassza a **Tagok importálása**lehetőséget.
1. A **Tömeges importálási csoport tagjai (Előnézet)** lapon válassza a **Letöltés** lehetőséget a szükséges csoporttagtulajdonságokkal rendelkező CSV-fájlsablon leigazolásához.

    ![A Tagok importálása parancs a csoport profillapján található](./media/groups-bulk-import-members/import-panel.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort a csoportba importálni kívánt csoporttagokhoz (a szükséges értékek **a tagobjektum-azonosító** vagy az **egyszerű felhasználó név).** Ezután mentse a fájlt.

   ![A CSV-fájl tartalmazza a tagok számára az importra szánt neveket és azonosítókat](./media/groups-bulk-import-members/csv-file.png)

1. A **Csoporttagok tömeges importálása (előnézet)** lap **Csv-fájl feltöltése**csoportjában keresse meg a fájlt. A fájl kijelölésétorként elindul a CSV-fájl érvényesítése.
1. A fájl tartalmának ellenőrzése után a tömeges importálási lap a **sikeresen feltöltött fájlt**jeleníti meg. Ha vannak hibák, ki kell javítania azokat, mielőtt elküldheti a feladatot.
1. Amikor a fájl megfelel az ellenőrzésen, válassza **a Küldés** lehetőséget az Azure tömeges művelet elindításához, amely importálja a csoporttagokat a csoportba.
1. Amikor az importálási művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeres volt.

## <a name="check-import-status"></a>Importálásállapotának ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **Tömeges művelet eredményei (előnézet)** lapon tekintheti meg.

   ![A Tömeges műveletek eredménylapja a tömeges kérelmek állapotát mutatja](./media/groups-bulk-import-members/bulk-center.png)

A tömeges művelet egyes sorainak részleteiért válassza ki a **# Sikeres**, **# Sikertelen**vagy Összes **kérelem** oszlop ban található értékeket. Ha hiba történt, a hiba okai jelennek meg a listában.

## <a name="bulk-import-service-limits"></a>Tömeges importálási szolgáltatási korlátok

A csoporttagok listájának importálásához minden tömeges tevékenység legfeljebb egy órán át futhat. Ez lehetővé teszi a legalább 40 000 tagot tartalmazó lista behozatalát.

## <a name="next-steps"></a>További lépések

- [Csoporttagok tömeges eltávolítása](groups-bulk-remove-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
- [Az összes csoport listájának letöltése](groups-bulk-download.md)
