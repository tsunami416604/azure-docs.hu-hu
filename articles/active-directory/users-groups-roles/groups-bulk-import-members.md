---
title: Feltöltés tömeges importálása a tagok csoportba való felvételéhez – Azure Active Directory | Microsoft Docs
description: Vegyen fel tömegesen a csoporttagokat a Azure Active Directory felügyeleti központban.
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
ms.openlocfilehash: 0335522b398c22fb395305b33bac5a56ba565ee2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911043"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Csoporttagok csoportos importálása (előzetes verzió) Azure Active Directory

A Azure Active Directory (Azure AD) portál használatával nagyszámú tagot adhat hozzá egy csoporthoz egy vesszővel tagolt (CSV) fájl használatával a csoporttagok tömeges importálásához.

> [!NOTE]
> Az Azure AD tömeges műveletei az Azure AD nyilvános előzetes funkciója, és minden fizetős Azure AD-licenccel elérhetők. Az előzetes verzió használati feltételeivel kapcsolatos további információkért lásd: a [Microsoft Azure előnézetekhez tartozó kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="bulk-import-service-limits"></a>Tömeges importálási szolgáltatás korlátai

A csoporttagok listájának importálására szolgáló tömeges tevékenységek akár egy óráig is futhatnak. Ez lehetővé teszi, hogy legalább 40 000 tagból álló listát importáljon.

## <a name="to-bulk-import-group-members"></a>Csoporttagok tömeges importálása

1. Jelentkezzen be [a Azure Portalba](https://portal.azure.com) a szervezet felhasználói rendszergazdai fiókjával. A csoport tulajdonosai a saját csoportok tagjainak tömeges importálását is elvégezheti.
1. Az Azure ad-ben válassza a **csoportok** > **minden csoport**elemet.
1. Nyissa meg azt a csoportot, amelyhez tagokat vesz fel, majd válassza a **tagok**lehetőséget.
1. A **tagok** lapon válassza a **tagok importálása** lehetőséget, hogy letöltse, frissítse és feltöltse a csoportba importálni kívánt TAGOKat felsoroló CSV-fájlt.

   ![A tagok importálása parancs a csoport profil lapján található.](./media/groups-bulk-import-members/import-panel.png)

## <a name="check-import-status"></a>Importálás állapotának keresése

Az összes függőben lévő tömeges kérelem állapotát a **tömeges művelet eredményei (előzetes verzió)** lapon tekintheti meg.

   ![A tömeges műveletek eredményei lapon a tömeges kérelem állapota látható](./media/groups-bulk-import-members/bulk-center.png)

## <a name="next-steps"></a>További lépések

- [Csoporttagok tömeges eltávolítása](groups-bulk-remove-members.md)
- [Csoport tagjainak letöltése](groups-bulk-download-members.md)
