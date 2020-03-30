---
title: Hozzáférési csomag elrejtése vagy törlése a jogosultságkezelésben – Azure AD
description: Megtudhatja, hogyan rejtheti el vagy törölheti a hozzáférési csomagot az Azure Active Directory jogosultságkezelésében.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261995"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag elrejtése vagy törlése az Azure AD jogosultságkezelésében

Az access csomagok alapértelmezés szerint észlelhetők. Ez azt jelenti, hogy ha egy házirend lehetővé teszi, hogy egy felhasználó kérje a hozzáférési csomagot, akkor automatikusan megjelenik a hozzáférési csomag a Saját hozzáférés portálon. A **Rejtett** beállítást azonban módosíthatja úgy, hogy a hozzáférési csomag ne jelenjenek meg a felhasználó Saját hozzáférés portálján.

Ez a cikk a hozzáférési csomagok elrejtésének és törlésének módját ismerteti.

## <a name="change-the-hidden-setting"></a>A Rejtett beállítás módosítása

A hozzáférési csomag **Rejtett** beállításának módosításához kövesse az alábbi lépéseket.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Az Áttekintés lapon kattintson a **Szerkesztés gombra.**

1. Állítsa be a **Rejtett** beállítást.

    Ha **nem,** a hozzáférési csomag megjelenik a felhasználó Saját hozzáférés portálján.

    Ha **az Igen**beállítás van megállítva, a hozzáférési csomag nem jelenik meg a felhasználó Saját hozzáférés portálján. A hozzáférési csomag megtekintésének egyetlen módja az, ha rendelkezik a hozzáférési csomaghoz való közvetlen **Saját hozzáférési portál hivatkozással.** További információt a [Hivatkozás megosztása hozzáférési csomag igényléséhez](entitlement-management-access-package-settings.md)című témakörben talál.

## <a name="delete-an-access-package"></a>Hozzáférési csomag törlése

A hozzáférési csomagok csak akkor törölhetők, ha nem rendelkezik aktív felhasználói hozzárendelésekkel. Hozzáférési csomag törléséhez kövesse az alábbi lépéseket.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson a **Hozzárendelések parancsra,** és távolítsa el a hozzáférést az összes felhasználó számára.

1. A bal oldali **menüben** kattintson az Áttekintés, majd a **Törlés parancsra.**

1. A megjelenő törlési üzenetben kattintson az **Igen**gombra.

## <a name="next-steps"></a>További lépések

- [Hozzáférési csomag hozzárendelésének megtekintése, hozzáadása és eltávolítása](entitlement-management-access-package-assignments.md)
- [Jelentések és naplók megtekintése](entitlement-management-reports.md)
