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
ms.openlocfilehash: 65a6a622a0d3fac7b28cc699d860068f12b780fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84728605"
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

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Szolgáltatási korlátok tömeges letöltése

A csoporttagok listájának letöltéséhez minden tömeges tevékenység legfeljebb egy óráig futhat. Ez lehetővé teszi, hogy letöltse legalább 500 000 tag listáját.

## <a name="next-steps"></a>További lépések

- [Csoporttagok tömeges importálása](groups-bulk-import-members.md)
- [Csoporttagok tömeges eltávolítása](groups-bulk-download-members.md)
