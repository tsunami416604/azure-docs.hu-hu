---
title: A csoportok listájának letöltése a Azure Active Directory portálon | Microsoft Docs
description: A csoport tulajdonságainak tömeges letöltése a Azure Active Directory Azure felügyeleti központban.
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
ms.openlocfilehash: 59983678c1b14d6aa87a7b500605e3abeb6a9b85
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81533538"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Csoportok listájának tömeges letöltése Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával tömegesen letöltheti a szervezet összes csoportjának listáját egy vesszővel tagolt (CSV) fájlba.

## <a name="to-download-a-list-of-groups"></a>A csoportok listájának letöltése

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com) egy rendszergazdai fiókkal a szervezeten belül.
1. Az Azure ad-ben válassza a **csoportok** > **letöltési csoportok**lehetőséget.
1. A **csoportok letöltése** lapon kattintson a **Start** gombra a csoportok listáját tartalmazó CSV-fájl fogadásához.

   ![A csoportok letöltése parancs a minden csoport oldalon található.](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Letöltés állapotának keresése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei** lapon tekintheti meg.

[![](media/groups-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Szolgáltatási korlátok tömeges letöltése

A csoportok listájának letöltésére szolgáló összes tömeges tevékenység legfeljebb egy óráig futhat. Ez lehetővé teszi, hogy legalább 300 000 csoportból álló listát töltsön le.

## <a name="next-steps"></a>További lépések

- [Csoporttagok tömeges eltávolítása](groups-bulk-remove-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
