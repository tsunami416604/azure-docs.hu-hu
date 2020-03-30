---
title: Hozzáférés-szabályozás leirányításának delegálása a csomagkezelők számára az Azure AD jogosultságkezelésben – Azure Active Directory
description: Megtudhatja, hogy miként delegálhatja a hozzáférés-irányítást az informatikai rendszergazdáktól a csomagkezelők és a projektmenedzserek számára, hogy saját maguk is kezelhessék a hozzáférést.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: efdc61b82a19cc5d370d6069e8c9dcd3ce5e8ae5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73174358"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Hozzáférés-szabályozás delegálása a csomagkezelők számára az Azure AD-jogosultságkezelésben

A katalógusban lévő hozzáférési csomagok létrehozásának és kezelésének delegálásához felhasználókat kell hozzáadnia a hozzáférési csomagkezelői szerepkörhöz. A hozzáférési csomagkezelőknek ismerniük kell, hogy a felhasználóknak hozzáférést kell kérniük a katalógusban lévő erőforrásokhoz. Ha például egy projekthez katalógust használnak, akkor a projektérdeklődők lehetnek a katalógus hozzáférési csomagkezelőjei.  Az Access csomagkezelők nem adhatnak hozzá erőforrásokat a katalógushoz, de kezelhetik a katalógusban lévő hozzáférési csomagokat és házirendeket.  A hozzáférési csomagkezelőre történő delegáláskor ez a személy felelős lehet a következőkért:

- Milyen szerepkörrel rendelkezik a felhasználó a katalógusban lévő erőforrásokhoz?
- Kinek lesz szüksége hozzáférésre
- Kinek kell jóváhagynia a hozzáférési kérelmeket
- A projekt élettartama

Ez a videó áttekintést nyújt arról, hogyan delegálhatja a hozzáférés-szabályozást a katalógustulajdonostól a csomagkezelőhöz.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Katalógustulajdonosként meghatalmazott a hozzáférési csomagkezelőben

A felhasználó hozzárendelése a hozzáférési csomagkezelői szerepkörhöz az alábbi lépésekkel:

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználó vagy katalógus tulajdonosa

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson a **Katalógusok parancsra,** majd nyissa meg azt a katalógust, amelyhez rendszergazdákat szeretne hozzáadni.

1. A bal oldali menüben kattintson a **Szerepkörök és rendszergazdák parancsra.**

    ![Katalógusok szerepkörök és rendszergazdák](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kattintson **a Hozzáférés-kezelők hozzáadása** gombra a szerepkörök tagjainak kiválasztásához.

1. A tagok hozzáadásához kattintson a **Kijelölés** gombra.

## <a name="remove-an-access-package-manager"></a>Hozzáférés-kezelő eltávolítása

A felhasználó access package manager szerepkörből való eltávolításához kövesse az alábbi lépéseket:

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználó vagy katalógus tulajdonosa

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson a **Katalógusok parancsra,** majd nyissa meg azt a katalógust, amelyhez rendszergazdákat szeretne hozzáadni.

1. A bal oldali menüben kattintson a **Szerepkörök és rendszergazdák parancsra.**

1. Jelölje be az eltávolítani kívánt hozzáférésicsomag-kezelőt.

1. Kattintson az **Eltávolítás** lehetőségre.

## <a name="next-steps"></a>További lépések

- [Új hozzáférési csomag létrehozása](entitlement-management-access-package-create.md)
