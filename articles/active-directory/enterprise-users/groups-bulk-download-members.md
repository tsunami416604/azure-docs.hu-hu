---
title: Csoportos tagságok listájának tömeges letöltése – Azure Active Directory portál | Microsoft Docs
description: A felhasználók tömeges hozzáadása az Azure felügyeleti központban.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec712d2f58c3028aa11d0b12132669648351bfb1
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375597"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Csoport tagjainak tömeges letöltése Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával tömegesen töltheti le a szervezet egy csoportjának tagjait vesszővel tagolt (CSV) fájlba.

## <a name="to-bulk-download-group-membership"></a>Csoporttagság tömeges letöltése

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com) a szervezet felhasználói rendszergazdai fiókjával. A csoport tulajdonosai a saját csoportok tagjait is tömegesen tölthetik le.
1. Az Azure ad-ben válassza a **csoportok**  >  **minden csoport**elemet.
1. Nyissa meg azt a csoportot, amelynek tagságát le szeretné tölteni, majd válassza a **tagok**lehetőséget.
1. A **tagok** lapon válassza a **tagok letöltése** lehetőséget, hogy letöltse a csoporttagokat felsoroló CSV-fájlt.

   ![A tagok letöltése parancs a csoport profil lapján található.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Letöltés állapotának keresése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei** lapon tekintheti meg.

[![A tömeges műveletek eredményei lapon található állapot keresése.](./media/groups-bulk-download-members/bulk-center.png)](./media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Szolgáltatási korlátok tömeges letöltése

A csoporttagok listájának letöltéséhez minden tömeges tevékenység legfeljebb egy óráig futhat. Ez lehetővé teszi, hogy letöltse legalább 500 000 tag listáját.

## <a name="next-steps"></a>Következő lépések

- [Csoporttagok tömeges importálása](groups-bulk-import-members.md)
- [Csoporttagok tömeges eltávolítása](groups-bulk-download-members.md)
