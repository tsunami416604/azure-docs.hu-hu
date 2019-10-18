---
title: Csoportos tagságok listájának tömeges letöltése – Azure Active Directory portál | Microsoft Docs
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
ms.openlocfilehash: 4e29aacb1357509e2b000a9d05c5ced8f9a30dce
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517158"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Csoport (előzetes verzió) csoportos letöltése Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával tömegesen töltheti le a szervezet egy csoportjának tagjait vesszővel tagolt (CSV) fájlba.

## <a name="to-bulk-download-group-membership"></a>Csoporttagság tömeges letöltése

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com) a szervezet felhasználói rendszergazdai fiókjával. A csoport tulajdonosai a saját csoportok tagjait is tömegesen tölthetik le.
1. Az Azure AD-ben válassza a **csoportok**  > **az összes csoport**elemet.
1. Nyissa meg azt a csoportot, amelynek tagságát le szeretné tölteni, majd válassza a **tagok**lehetőséget.
1. A **tagok** lapon válassza a **tagok letöltése** lehetőséget, hogy letöltse a csoporttagokat felsoroló CSV-fájlt.

   ![A tagok letöltése parancs a csoport profil lapján található.](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Letöltés állapotának keresése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei (előzetes verzió)** lapon tekintheti meg.

   ![A tömeges műveletek eredményei lapon a tömeges kérelem állapota látható](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Szolgáltatási korlátok tömeges letöltése

A csoporttagok listájának letöltéséhez minden tömeges tevékenység legfeljebb egy óráig futhat. Ez lehetővé teszi, hogy letöltse legalább 500 000 tag listáját.

## <a name="next-steps"></a>Következő lépések

- [Csoporttagok tömeges importálása](groups-bulk-import-members.md)
- [Csoporttagok tömeges eltávolítása](groups-bulk-download-members.md)
