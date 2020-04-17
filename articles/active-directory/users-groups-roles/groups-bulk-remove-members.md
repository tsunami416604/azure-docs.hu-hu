---
title: A csoporttagok tömeges eltávolítása CSV-fájl feltöltésével – Azure Active Directory | Microsoft dokumentumok
description: Távolítsa el a csoporttagokat tömeges műveletekben az Azure Felügyeleti központban.
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
ms.openlocfilehash: 1e6d0752245e3864a8ad25efd5181d5cc1eec7ae
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533255"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Csoporttagok tömeges eltávolítása az Azure Active Directoryban

Az Azure Active Directory (Azure AD) portál használatával eltávolíthatja a tagok nagy számú tagja egy csoport segítségével egy vesszővel tagolt értékek (CSV) fájl tömeges eltávolítása csoporttagok.

## <a name="to-bulk-remove-group-members"></a>Csoporttagok tömeges eltávolítása

1. Jelentkezzen be [az Azure Portalon](https://portal.azure.com) egy felhasználói rendszergazdai fiókkal a szervezetben. A csoporttulajdonosok tömegesen is eltávolíthatják a saját csoporttagjaikat.
1. Az Azure AD-ben válassza **a Csoportok** > **minden csoport lehetőséget.**
1. Nyissa meg azt a csoportot, amelyből tagokat távolít el, majd válassza a **Tagok**lehetőséget.
1. A **Tagok** lapon válassza a **Tagok eltávolítása**lehetőséget.
1. A **Csoporttagok tömeges eltávolítása** lapon válassza a **Letöltés** lehetőséget a szükséges csoporttag-tulajdonságokkal rendelkező CSV-fájlsablon leigazolásához.

   ![A Tagok eltávolítása parancs a csoport profillapján található](./media/groups-bulk-remove-members/remove-panel.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden olyan csoporttaghoz, akiről el szeretné távolítani a csoportot (a szükséges értékek a tagobjektum-azonosító vagy az egyszerű felhasználónév). Ezután mentse a fájlt.

   ![A CSV-fájl neveket és azonosítókat tartalmaz a tagok számára, hogy](./media/groups-bulk-remove-members/csv-file.png)

1. A **Csoporttagok tömeges eltávolítása** lap **Csv-fájl feltöltése**csoportjában keresse meg a fájlt. A fájl kijelölésétorként elindul a CSV-fájl érvényesítése.
1. A fájl tartalmának ellenőrzése után a tömeges importálási lap a **sikeresen feltöltött fájlt**jeleníti meg. Ha vannak hibák, ki kell javítania azokat, mielőtt elküldheti a feladatot.
1. Amikor a fájl megfelel az ellenőrzésen, válassza **a Küldés** lehetőséget az Azure tömeges művelet elindításához, amely eltávolítja a csoporttagokat a csoportból.
1. Amikor az eltávolítási művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeres volt.

## <a name="check-removal-status"></a>Eltávolítási állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **Tömeges művelet eredménylapján láthatja.**

[![](media/groups-bulk-remove-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-remove-members/bulk-center.png#lightbox)

A tömeges művelet egyes sorainak részleteiért válassza ki a **# Sikeres**, **# Sikertelen**vagy Összes **kérelem** oszlop ban található értékeket. Ha hiba történt, a hiba okai jelennek meg a listában.

## <a name="bulk-removal-service-limits"></a>Tömeges eltávolítási szolgáltatás korlátai

A csoporttagok listájának eltávolításához minden egyes tömeges tevékenység legfeljebb egy órán át futhat. Ez lehetővé teszi a legalább 40 000 tagot tartalmazó lista eltávolítását.

## <a name="next-steps"></a>További lépések

- [Tömeges importálási csoport tagjai](groups-bulk-import-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
- [Az összes csoport listájának letöltése](groups-bulk-download.md)
