---
title: Tömeges enletöltő csoporttagsági lista – Azure Active Directory portál | Microsoft dokumentumok
description: Felhasználók tömeges hozzáadása az Azure Felügyeleti központban.
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
ms.openlocfilehash: 3faca8d1a2538ed03a917d6db8d54323fe626369
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533680"
---
# <a name="bulk-download-members-of-a-group-in-azure-active-directory"></a>Csoport tömeges letöltése az Azure Active Directoryban

Az Azure Active Directory (Azure AD) portál használatával tömegesen letöltheti a szervezet egy csoportjának tagjait egy vesszővel tagolt értékfájlba (CSV).

## <a name="to-bulk-download-group-membership"></a>Csoporttagság tömeges letöltése

1. Jelentkezzen be [az Azure Portalon](https://portal.azure.com) egy felhasználói rendszergazdai fiókkal a szervezetben. A csoporttulajdonosok tömegesen is letölthetik a saját csoporttagjaikat.
1. Az Azure AD-ben válassza **a Csoportok** > **minden csoport lehetőséget.**
1. Nyissa meg azt a csoportot, amelynek tagságát le szeretné tölteni, majd válassza a **Tagok**lehetőséget.
1. A **Tagok** lapon válassza a **Tagok letöltése** lehetőséget a csoporttagokat felsoroló CSV-fájl letöltéséhez.

   ![A Tagok letöltése parancs a csoport profillapján található](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Letöltési állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **Tömeges művelet eredménylapján láthatja.**

[![](media/groups-bulk-download-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-download-members/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Tömeges letöltési szolgáltatás korlátai

A csoporttagok listájának letöltéséhez minden tömeges tevékenység legfeljebb egy órán át futhat. Ez lehetővé teszi, hogy letölt egy listát legalább 500.000 tagja van.

## <a name="next-steps"></a>További lépések

- [Tömeges importálási csoport tagjai](groups-bulk-import-members.md)
- [Csoporttagok tömeges eltávolítása](groups-bulk-download-members.md)
