---
title: Csoporttagok tömeges eltávolítása egy CSV-fájl feltöltésével – Azure Active Directory | Microsoft Docs
description: A felhasználók tömeges hozzáadása az Azure felügyeleti központban.
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
ms.openlocfilehash: 08708c23f9c8f4c4a8fc9f2f0aa5cd20d8333a42
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146378"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Csoporttagok tömeges eltávolítása (előzetes verzió) Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával számos tagot eltávolíthat egy csoportból egy vesszővel tagolt (CSV) fájl használatával a csoporttagok tömeges eltávolításához.

> [!NOTE]
> Az Azure AD tömeges műveletei az Azure AD nyilvános előzetes funkciója, és minden fizetős Azure AD-licenccel elérhetők. Az előzetes verzió használati feltételeivel kapcsolatos további információkért lásd: a [Microsoft Azure előnézetekhez tartozó kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="to-bulk-remove-group-members"></a>Csoporttagok tömeges eltávolítása

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com) a szervezet felhasználói rendszergazdai fiókjával. A csoport tulajdonosai a saját csoportok tagjainak tömeges eltávolítására is jogosultak.
1. Az Azure ad-ben válassza a **csoportok** > **minden csoport**elemet.
1. Nyissa meg azt a csoportot, amelyből el kívánja távolítani a tagokat, majd válassza a **tagok**elemet.
1. A **tagok** lapon válassza a **tagok eltávolítása**lehetőséget.
1. A csoporttagok **eltávolítása (előzetes verzió)** lapon válassza a **Letöltés** lehetőséget a CSV-sablonfájl a szükséges csoporttagok tulajdonságaival való beszerzéséhez.

   ![A tagok eltávolítása parancs a csoport profil lapján található.](./media/groups-bulk-remove-members/remove-panel.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden olyan csoporttag számára, amelyet el szeretne távolítani a csoportból (a kötelező értékek a tag objektum azonosítója vagy egyszerű felhasználónév). Ezután mentse a fájlt.

   ![A CSV-fájl az eltávolítandó tagok nevét és azonosítóit tartalmazza](./media/groups-bulk-remove-members/csv-file.png)

1. A **csoporttagok eltávolítása (előzetes verzió)** lapon, a CSV- **fájl feltöltése**területen keresse meg a fájlt. A fájl kiválasztásakor elindul a. csv-fájl érvényesítése.
1. A fájl tartalmának ellenőrzésekor a tömeges importálás lap megjeleníti a **fájl feltöltésének sikerességét**. Ha hibák léptek fel, ezeket a feladatok elküldése előtt ki kell javítania.
1. Ha a fájl átadja az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget az Azure tömeges művelet elindításához, amely eltávolítja a csoport tagjait a csoportból.
1. Amikor az eltávolítási művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeresen befejeződött.

## <a name="check-removal-status"></a>Eltávolítás állapotának ellenõrzése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei (előzetes verzió)** lapon tekintheti meg.

   ![A tömeges műveletek eredményei lapon a tömeges kérelem állapota látható](./media/groups-bulk-remove-members/bulk-center.png)

A tömeges műveletben szereplő egyes sorokra vonatkozó részletekért válassza ki a **# sikeres**, **# sikertelen**vagy az **összes kérelem** oszlopban szereplő értékeket. Ha hiba történt, a hiba okai lesznek felsorolva.

## <a name="bulk-removal-service-limits"></a>Tömeges eltávolítási szolgáltatás korlátai

Minden tömeges tevékenység, amelyből a csoporttagok listája akár egy óráig is futtatható. Ez lehetővé teszi, hogy legalább 40 000 tagot tartalmazó lista legyen eltávolítva.

## <a name="next-steps"></a>További lépések

- [Csoporttagok tömeges importálása](groups-bulk-import-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
- [Az összes csoport listájának letöltése](groups-bulk-download.md)
