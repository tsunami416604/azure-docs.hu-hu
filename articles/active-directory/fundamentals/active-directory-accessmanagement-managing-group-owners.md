---
title: További lépések a csoportok – az Azure AD alkalmazáshozzáférés-kezeléshez |} A Microsoft Docs
description: Hogyan speciális – a felhasználó a biztonsági csoportok és hogyan használhatja ezeket a csoportokat egy erőforráshoz való hozzáférés kezelése kezelése.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/12/2017
ms.author: lizross
ms.custom: it-pro
ms.openlocfilehash: bdc8754253ce2567d957b4d6240fe52242aea2ea
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449884"
---
# <a name="managing-owners-for-a-group"></a>Egy csoport tulajdonosainak kezelése
Miután az erőforrás tulajdonosa hozzáférés van rendelve egy erőforrást az Azure AD-csoportok, a csoport tagságának a csoport tulajdonosa kezeli. Az erőforrás tulajdonosa hatékonyan a felhasználók hozzárendelése az erőforrást a csoport tulajdonosa engedélyt ad.

## <a name="add-an-owner-to-a-group"></a>Tulajdonos hozzáadása csoporthoz

1. Az a [Azure AD felügyeleti központ](https://aad.portal.azure.com)válassza **felhasználók és csoportok**.
2. Válassza ki **összes csoport**, majd nyissa meg a csoport tulajdonosai hozzáadni kívánt.
3. Válassza ki **tulajdonosok hozzáadása**.
4. Az a **tulajdonosok hozzáadása** lapon, válassza ki a felhasználót, hogy azt szeretné, adja hozzá a csoport tulajdonosa, és ellenőrizze, hogy ez a név a **kijelölt** ablaktáblán.

## <a name="remove-an-owner-from-a-group"></a>Tulajdonos eltávolítása egy csoportból

1. Az a [Azure AD felügyeleti központ](https://aad.portal.azure.com)válassza **felhasználók és csoportok**.
2. Válassza ki **összes csoport**, majd nyissa meg a csoportot, amelyből el kívánja távolítani tulajdonosai.
3. Válassza ki a **tulajdonosok** fülre.
4. Válassza ki a tulajdonos eltávolítása a csoportból, és válassza ki a kívánt **eltávolítása**.

## <a name="additional-information"></a>További információ
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](../users-groups-roles/groups-settings-cmdlets.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](../active-directory-apps-index.md)
* [Mi az az Azure Active Directory?](active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../connect/active-directory-aadconnect.md)
