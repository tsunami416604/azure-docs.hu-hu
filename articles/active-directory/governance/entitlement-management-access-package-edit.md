---
title: Hozzáférési csomag elrejtése vagy törlése a jogosultságok kezelésében – Azure AD
description: Megtudhatja, hogyan rejtheti el vagy törölheti a hozzáférési csomagokat Azure Active Directory jogosultságok kezelésében.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79261995"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag elrejtése vagy törlése az Azure AD-jogosultságok kezelésében

A hozzáférési csomagok alapértelmezés szerint felderíthetők. Ez azt jelenti, hogy ha egy házirend lehetővé teszi a felhasználó számára a hozzáférési csomag kérését, automatikusan a saját hozzáférési portálon megjelenő hozzáférési csomagot fogják látni. A **rejtett** beállítást azonban módosíthatja úgy, hogy a hozzáférési csomag ne szerepeljen a felhasználó saját hozzáférési portálján.

Ez a cikk egy hozzáférési csomag elrejtését és törlését ismerteti.

## <a name="change-the-hidden-setting"></a>A rejtett beállítás módosítása

Kövesse az alábbi lépéseket egy hozzáférési csomag **rejtett** beállításának módosításához.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Az Áttekintés lapon kattintson a **Szerkesztés**elemre.

1. Állítsa be a **rejtett** beállítást.

    Ha a **nem**értékre van állítva, a hozzáférési csomag a felhasználó saját hozzáférési portálján jelenik meg.

    Ha az **Igen**értékre van állítva, a hozzáférési csomag nem jelenik meg a felhasználó saját hozzáférési portálján. Az egyetlen mód, amikor a felhasználó megtekintheti a hozzáférési csomagot, ha a közvetlen hozzáférési portál a hozzáférési csomagra **mutató hivatkozást** tartalmaz. További információ: [megosztási hivatkozás a hozzáférési csomag igényléséhez](entitlement-management-access-package-settings.md).

## <a name="delete-an-access-package"></a>Hozzáférési csomag törlése

Hozzáférési csomag csak akkor törölhető, ha nem rendelkezik aktív felhasználói hozzárendelésekkel. Egy hozzáférési csomag törléséhez kövesse az alábbi lépéseket.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson a **hozzárendelések** elemre, és távolítsa el az összes felhasználó hozzáférését.

1. A bal oldali menüben kattintson az **Áttekintés** , majd a **Törlés**elemre.

1. A megjelenő törlési üzenetben kattintson az **Igen**gombra.

## <a name="next-steps"></a>További lépések

- [Hozzáférési csomag hozzárendeléseinek megtekintése, hozzáadása és eltávolítása](entitlement-management-access-package-assignments.md)
- [Jelentések és naplók megtekintése](entitlement-management-reports.md)
