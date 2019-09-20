---
title: Feltöltés tömeges importálása a tagok csoportba való felvételéhez – Azure Active Directory | Microsoft Docs
description: Vegyen fel tömegesen a csoporttagokat a Azure Active Directory felügyeleti központban.
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
ms.openlocfilehash: 4412bc9ce8d78b5810b25b60724575af66774127
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146245"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Csoporttagok csoportos importálása (előzetes verzió) Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával nagyszámú tagot adhat hozzá egy csoporthoz egy vesszővel tagolt (CSV) fájl használatával a csoporttagok tömeges importálásához.

> [!NOTE]
> Az Azure AD tömeges műveletei az Azure AD nyilvános előzetes funkciója, és minden fizetős Azure AD-licenccel elérhetők. Az előzetes verzió használati feltételeivel kapcsolatos további információkért lásd: a [Microsoft Azure előnézetekhez tartozó kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="to-bulk-import-group-members"></a>Csoporttagok tömeges importálása

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com) a szervezet felhasználói rendszergazdai fiókjával. A csoport tulajdonosai a saját csoportok tagjainak tömeges importálását is elvégezheti.
1. Az Azure ad-ben válassza a **csoportok** > **minden csoport**elemet.
1. Nyissa meg azt a csoportot, amelyhez tagokat vesz fel, majd válassza a **tagok**lehetőséget.
1. A **tagok** lapon válassza a **tagok importálása**lehetőséget.
1. A **csoportos importálás csoporttagok (előzetes verzió)** lapon válassza a **Letöltés** lehetőséget a CSV-sablonfájl a szükséges csoporttagok tulajdonságaival való beszerzéséhez.

    ![A tagok importálása parancs a csoport profil lapján található.](./media/groups-bulk-import-members/import-panel.png)

1. Nyissa meg a CSV-fájlt, és adjon hozzá egy sort minden olyan csoporttag számára, amelyet importálni szeretne a csoportba (a kötelező értékek vagy **tag-azonosítók** vagy **egyszerű felhasználónév**). Ezután mentse a fájlt.

   ![A CSV-fájl az importálandó tagok nevét és azonosítóit tartalmazza.](./media/groups-bulk-import-members/csv-file.png)

1. A **csoportos importálási csoporttagok (előzetes verzió)** lapon, a **CSV-fájl feltöltése**területen keresse meg a fájlt. A fájl kiválasztásakor elindul a CSV-fájl érvényesítése.
1. A fájl tartalmának ellenőrzésekor a tömeges importálás lap megjeleníti a **fájl feltöltésének sikerességét**. Ha hibák léptek fel, ezeket a feladatok elküldése előtt ki kell javítania.
1. Ha a fájl érvényesíti az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget az Azure tömeges művelet elindításához, amely importálja a csoporttagokat a csoportba.
1. Amikor az importálási művelet befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeresen befejeződött.

## <a name="check-import-status"></a>Importálás állapotának keresése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei (előzetes verzió)** lapon tekintheti meg.

   ![A tömeges műveletek eredményei lapon a tömeges kérelem állapota látható](./media/groups-bulk-import-members/bulk-center.png)

A tömeges műveletben szereplő egyes sorokra vonatkozó részletekért válassza ki a **# sikeres**, **# sikertelen**vagy az **összes kérelem** oszlopban szereplő értékeket. Ha hiba történt, a hiba okai lesznek felsorolva.

## <a name="bulk-import-service-limits"></a>Tömeges importálási szolgáltatás korlátai

A csoporttagok listájának importálására szolgáló tömeges tevékenységek akár egy óráig is futhatnak. Ez lehetővé teszi, hogy legalább 40 000 tagból álló listát importáljon.

## <a name="next-steps"></a>További lépések

- [Csoporttagok tömeges eltávolítása](groups-bulk-remove-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
- [Az összes csoport listájának letöltése](groups-bulk-download.md)
