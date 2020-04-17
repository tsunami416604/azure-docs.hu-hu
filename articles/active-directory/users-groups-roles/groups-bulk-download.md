---
title: Csoportok listájának letöltése az Azure Active Directory portálon | Microsoft dokumentumok
description: A csoport tulajdonságok tömeges letöltése az Azure Active Directory Azure Felügyeleti központban.
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533538"
---
# <a name="bulk-download-a-list-of-groups-in-azure-active-directory"></a>Csoportok listájának tömeges letöltése az Azure Active Directoryban

Az Azure Active Directory (Azure AD) portál használatával tömegesen letöltheti a szervezet összes csoportjának listáját egy vesszővel tagolt értékfájlba (CSV).

## <a name="to-download-a-list-of-groups"></a>Csoportok listájának letöltése

1. Jelentkezzen be [az Azure Portalra](https://portal.azure.com) egy rendszergazdai fiókkal a szervezetben.
1. Az Azure AD-ben válassza a **Csoportok** > **letöltési csoportok lehetőséget.**
1. A **Csoportok letöltése** lapon válassza a **Start** lehetőséget a csoportokat felsoroló CSV-fájl fogadásához.

   ![A letöltési csoportok parancs a Minden csoport lapon található](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Letöltési állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **Tömeges művelet eredménylapján láthatja.**

[![](media/groups-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Tömeges letöltési szolgáltatás korlátai

A csoportlista letöltéséhez minden tömeges tevékenység legfeljebb egy órán át futhat. Ez lehetővé teszi legalább 300 000 csoport ból álló lista letöltését.

## <a name="next-steps"></a>További lépések

- [Csoporttagok tömeges eltávolítása](groups-bulk-remove-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
