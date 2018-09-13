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
ms.date: 09/11/2018
ms.author: lizross
ms.custom: it-pro
ms.openlocfilehash: d84c3b556f27366c249382a37e0a8227726110cf
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714684"
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
* [Alkalmazások kezelése az Azure Active Directoryban](../manage-apps/what-is-application-management.md)
* [Mi az az Azure Active Directory?](active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../connect/active-directory-aadconnect.md)
