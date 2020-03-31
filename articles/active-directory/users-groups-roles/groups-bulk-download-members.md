---
title: Tömeges enletöltő csoporttagsági lista – Azure Active Directory portál | Microsoft dokumentumok
description: Felhasználók tömeges hozzáadása az Azure Felügyeleti központban.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517158"
---
# <a name="bulk-download-members-of-a-group-preview-in-azure-active-directory"></a>Csoport tagjainak tömeges letöltése (előzetes verzió) az Azure Active Directoryban

Az Azure Active Directory (Azure AD) portál használatával tömegesen letöltheti a szervezet egy csoportjának tagjait egy vesszővel tagolt értékfájlba (CSV).

## <a name="to-bulk-download-group-membership"></a>Csoporttagság tömeges letöltése

1. Jelentkezzen be [az Azure Portalon](https://portal.azure.com) egy felhasználói rendszergazdai fiókkal a szervezetben. A csoporttulajdonosok tömegesen is letölthetik a saját csoporttagjaikat.
1. Az Azure AD-ben válassza **a Csoportok** > **minden csoport lehetőséget.**
1. Nyissa meg azt a csoportot, amelynek tagságát le szeretné tölteni, majd válassza a **Tagok**lehetőséget.
1. A **Tagok** lapon válassza a **Tagok letöltése** lehetőséget a csoporttagokat felsoroló CSV-fájl letöltéséhez.

   ![A Tagok letöltése parancs a csoport profillapján található](./media/groups-bulk-download-members/download-panel.png)

## <a name="check-download-status"></a>Letöltési állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **Tömeges művelet eredményei (előnézet)** lapon tekintheti meg.

   ![A Tömeges műveletek eredménylapja a tömeges kérelmek állapotát mutatja](./media/groups-bulk-download-members/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Tömeges letöltési szolgáltatás korlátai

A csoporttagok listájának letöltéséhez minden tömeges tevékenység legfeljebb egy órán át futhat. Ez lehetővé teszi, hogy letölt egy listát legalább 500.000 tagja van.

## <a name="next-steps"></a>További lépések

- [Tömeges importálási csoport tagjai](groups-bulk-import-members.md)
- [Csoporttagok tömeges eltávolítása](groups-bulk-download-members.md)
