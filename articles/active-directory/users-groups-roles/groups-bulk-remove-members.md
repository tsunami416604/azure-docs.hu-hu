---
title: A csoporttagok tömeges eltávolítása csv-fájl feltöltésével – Azure Active Directory | Microsoft dokumentumok
description: Felhasználók tömeges hozzáadása az Azure Felügyeleti központban.
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
ms.openlocfilehash: 9d384ea4749e2d0bc7edf8df7ac0508566f2f76b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517104"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Csoporttagok tömeges eltávolítása (előzetes verzió) az Azure Active Directoryban

Az Azure Active Directory (Azure AD) portál használatával eltávolíthatja a tagok nagy számú tagja egy csoport segítségével egy vesszővel tagolt értékek (CSV) fájl tömeges eltávolítása csoporttagok.

## <a name="to-bulk-remove-group-members"></a>Csoporttagok tömeges eltávolítása

1. Jelentkezzen be [az Azure Portalon](https://portal.azure.com) egy felhasználói rendszergazdai fiókkal a szervezetben. A csoporttulajdonosok tömegesen is eltávolíthatják a saját csoporttagjaikat.
1. Az Azure AD-ben válassza **a Csoportok** > **minden csoport lehetőséget.**
1. Nyissa meg azt a csoportot, amelyből tagokat távolít el, majd válassza a **Tagok**lehetőséget.
1. A **Tagok** lapon válassza a **Tagok eltávolítása**lehetőséget.
1. A **Csoporttagok tömeges eltávolítása (előnézet)** lapon válassza a **Letöltés** lehetőséget a szükséges csoporttagtulajdonságokkal rendelkező CSV-fájlsablon leigazolásához.

   ![A Tagok eltávolítása parancs a csoport profillapján található](./media/groups-bulk-remove-members/remove-panel.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden olyan csoporttaghoz, akiről el szeretné távolítani a csoportot (a szükséges értékek a tagobjektum-azonosító vagy az egyszerű felhasználónév). Ezután mentse a fájlt.

   ![A CSV-fájl neveket és azonosítókat tartalmaz a tagok számára, hogy](./media/groups-bulk-remove-members/csv-file.png)

1. A **Csoporttagok tömeges eltávolítása (előnézet)** lap **Csv-fájl feltöltése**csoportjában keresse meg a fájlt. A fájl kijelölésétorként megkezdődik a .csv fájl érvényesítése.
1. A fájl tartalmának ellenőrzése után a tömeges importálási lap a **sikeresen feltöltött fájlt**jeleníti meg. Ha vannak hibák, ki kell javítania azokat, mielőtt elküldheti a feladatot.
1. Amikor a fájl megfelel az ellenőrzésen, válassza **a Küldés** lehetőséget az Azure tömeges művelet elindításához, amely eltávolítja a csoporttagokat a csoportból.
1. Amikor az eltávolítási művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeres volt.

## <a name="check-removal-status"></a>Eltávolítási állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **Tömeges művelet eredményei (előnézet)** lapon tekintheti meg.

   ![A Tömeges műveletek eredménylapja a tömeges kérelmek állapotát mutatja](./media/groups-bulk-remove-members/bulk-center.png)

A tömeges művelet egyes sorainak részleteiért válassza ki a **# Sikeres**, **# Sikertelen**vagy Összes **kérelem** oszlop ban található értékeket. Ha hiba történt, a hiba okai jelennek meg a listában.

## <a name="bulk-removal-service-limits"></a>Tömeges eltávolítási szolgáltatás korlátai

A csoporttagok listájának eltávolításához minden egyes tömeges tevékenység legfeljebb egy órán át futhat. Ez lehetővé teszi a legalább 40 000 tagot tartalmazó lista eltávolítását.

## <a name="next-steps"></a>További lépések

- [Tömeges importálási csoport tagjai](groups-bulk-import-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
- [Az összes csoport listájának letöltése](groups-bulk-download.md)
