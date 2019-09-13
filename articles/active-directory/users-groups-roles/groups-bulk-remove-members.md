---
title: Csoporttagok tömeges eltávolítása egy CSV-fájl feltöltésével – Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: b153db3570f10ad5ad130dedd0bd20fe22776ed6
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911032"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Csoporttagok tömeges eltávolítása (előzetes verzió) Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával számos tagot eltávolíthat egy csoportból egy vesszővel tagolt (CSV) fájl használatával a csoporttagok tömeges eltávolításához.

> [!NOTE]
> Az Azure AD tömeges műveletei az Azure AD nyilvános előzetes funkciója, és minden fizetős Azure AD-licenccel elérhetők. Az előzetes verzió használati feltételeivel kapcsolatos további információkért lásd: a [Microsoft Azure előnézetekhez tartozó kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bulk-removal-service-limits"></a>Tömeges eltávolítási szolgáltatás korlátai

Minden tömeges tevékenység, amelyből a csoporttagok listája akár egy óráig is futtatható. Ez lehetővé teszi, hogy legalább 40 000 tagot tartalmazó lista legyen eltávolítva.

## <a name="to-bulk-remove-group-members"></a>Csoporttagok tömeges eltávolítása

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com) a szervezet felhasználói rendszergazdai fiókjával. A csoport tulajdonosai a saját csoportok tagjainak tömeges eltávolítására is jogosultak.
1. Az Azure ad-ben válassza a **csoportok** > **minden csoport**elemet.
1. Nyissa meg azt a csoportot, amelyből el kívánja távolítani a tagokat, majd válassza a **tagok**elemet.
1. A **tagok** lapon válassza a **tagok eltávolítása** lehetőséget, hogy letöltse, frissítse és feltöltse egy CSV-fájlt, amely felsorolja azokat a tagokat, amelyeket el szeretne távolítani a csoportból.

   ![A tagok eltávolítása parancs a csoport profil lapján található.](./media/groups-bulk-remove-members/remove-panel.png)

## <a name="check-removal-status"></a>Eltávolítás állapotának ellenõrzése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei (előzetes verzió)** lapon tekintheti meg.

   ![A tömeges műveletek eredményei lapon a tömeges kérelem állapota látható](./media/groups-bulk-remove-members/bulk-center.png)

## <a name="next-steps"></a>További lépések

- [Csoporttagok tömeges importálása](groups-bulk-import-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
