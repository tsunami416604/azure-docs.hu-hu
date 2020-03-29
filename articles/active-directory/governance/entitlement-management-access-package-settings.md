---
title: Megosztási hivatkozás hozzáférési csomag igényléséhez az Azure AD jogosultságkezelésében – Azure Active Directory
description: Megtudhatja, hogyan oszthat meg hivatkozást egy hozzáférési csomag igényléséhez az Azure Active Directory jogosultságkezelésében.
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
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968763"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Megosztási hivatkozás hozzáférési csomag igényléséhez az Azure AD jogosultságkezelésében

A címtárban lévő legtöbb felhasználó bejelentkezhet a Saját Hozzáférés portálra, és automatikusan megtekintheti a kért hozzáférési csomagok listáját. A külső üzleti partnerek olyan felhasználói számára azonban, akik még nem szerepelnek a címtárban, el kell küldenie nekik egy hivatkozást, amelyet hozzáférési csomag igényléséhez használhatnak. 

Mindaddig, amíg a hozzáférési csomag katalógusa engedélyezve van a [külső felhasználók számára,](entitlement-management-catalog-create.md) és rendelkezik [a külső felhasználó címtárának házirendjével,](entitlement-management-access-package-request-policy.md)a külső felhasználó a Saját hozzáférés portál hivatkozásával kérheti a hozzáférési csomagot.

## <a name="share-link-to-request-an-access-package"></a>Hozzáférési csomag igényléséhez hivatkozás megosztása

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Az Áttekintés lapon másolja a **Saját hozzáférés portál hivatkozását**.

    ![Access-csomag áttekintése – A Saját Hozzáférés portál hivatkozása](./media/entitlement-management-shared/my-access-portal-link.png)

    Fontos, hogy a teljes Hozzáférés portálhivatkozást másolja, amikor belső üzleti partnernek küldi. Ez biztosítja, hogy a partner hozzáférést kap a címtár portálján, hogy a kérelmet. A kapcsolat `myaccess`a programmal kezdődik, tartalmaz egy címtár-emlékeztetőt, és egy hozzáférési csomag azonosítójával végződik.  (Az Egyesült Államok kormánya számára a My `myaccess.microsoft.us`Access portál hivatkozásának tartománya a .)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Küldje el e-mailben a linket külső üzleti partnerének. Megoszthatják a hivatkozást a felhasználókkal, hogy kérje a hozzáférési csomagot.

## <a name="next-steps"></a>További lépések

- [Hozzáférés kérése hozzáférési csomaghoz](entitlement-management-request-access.md)
- [Hozzáférési kérelmek jóváhagyása vagy megtagadása](entitlement-management-request-approve.md)