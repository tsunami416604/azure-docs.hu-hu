---
title: A csoportok listájának letöltése a Azure Active Directory portálon | Microsoft Docs
description: A csoport tulajdonságainak tömeges letöltése a Azure Active Directory Azure felügyeleti központban.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bf7d0a650d73127a87475d4fcda9cff266040eb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650868"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Csoportok listájának tömeges letöltése Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával tömegesen letöltheti a szervezet összes csoportjának listáját egy vesszővel tagolt (CSV) fájlba.

## <a name="to-download-a-list-of-groups"></a>A csoportok listájának letöltése

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com) egy rendszergazdai fiókkal a szervezeten belül.
1. Az Azure ad-ben válassza a **csoportok**  >  **letöltési csoportok** lehetőséget.
1. A **csoportok letöltése** lapon kattintson a **Start** gombra a csoportok listáját tartalmazó CSV-fájl fogadásához.

   ![A csoportok letöltése parancs a minden csoport oldalon található.](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Letöltés állapotának keresése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei** lapon tekintheti meg.

[![A tömeges műveletek eredményei lapon található állapot keresése.](./media/groups-bulk-download/bulk-center.png)](./media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Szolgáltatási korlátok tömeges letöltése

A csoportok listájának letöltésére szolgáló összes tömeges tevékenység legfeljebb egy óráig futhat. Ez lehetővé teszi, hogy legalább 300 000 csoportból álló listát töltsön le.

## <a name="next-steps"></a>Következő lépések

- [Csoporttagok tömeges eltávolítása](groups-bulk-remove-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
