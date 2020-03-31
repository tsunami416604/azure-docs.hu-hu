---
title: Csoportok listájának letöltése az Azure Active Directory portálon | Microsoft dokumentumok
description: A csoport tulajdonságok tömeges letöltése az Azure Active Directory Azure Felügyeleti központban.
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
ms.openlocfilehash: 4b08e807e179270b63ca81d3777c230c3e129c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517153"
---
# <a name="bulk-download-a-list-of-groups-preview-in-azure-active-directory"></a>Csoportok listájának tömeges letöltése (előzetes verzió) az Azure Active Directoryban

Az Azure Active Directory (Azure AD) portál használatával tömegesen letöltheti a szervezet összes csoportjának listáját egy vesszővel tagolt értékfájlba (CSV).

## <a name="to-download-a-list-of-groups"></a>Csoportok listájának letöltése

1. Jelentkezzen be [az Azure Portalra](https://portal.azure.com) egy rendszergazdai fiókkal a szervezetben.
1. Az Azure AD-ben válassza a **Csoportok** > **letöltési csoportok lehetőséget.**
1. A **Csoportok letöltése** lapon válassza a **Start** lehetőséget a csoportokat felsoroló CSV-fájl fogadásához.

   ![A letöltési csoportok parancs a Minden csoport lapon található](./media/groups-bulk-download/bulk-download.png)

## <a name="check-download-status"></a>Letöltési állapot ellenőrzése

Az összes függőben lévő tömeges kérelem állapotát a **Tömeges művelet eredményei (előnézet)** lapon tekintheti meg.

   ![A Tömeges műveletek eredménylapja a tömeges kérelmek állapotát mutatja](./media/groups-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Tömeges letöltési szolgáltatás korlátai

A csoportlista letöltéséhez minden tömeges tevékenység legfeljebb egy órán át futhat. Ez lehetővé teszi legalább 300 000 csoport ból álló lista letöltését.

## <a name="next-steps"></a>További lépések

- [Csoporttagok tömeges eltávolítása](groups-bulk-remove-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
