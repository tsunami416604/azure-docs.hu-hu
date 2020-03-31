---
title: Csoport használata a SaaS-alkalmazásokhoz való hozzáférés kezeléséhez - Azure AD | Microsoft dokumentumok
description: Az Azure Active Directory ban lévő csoportok használata az Azure Active Directoryval integrált SaaS-alkalmazásokhoz való hozzáférés hozzárendeléséhez.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51375f057543c86fe021822eb9722ffd1be16804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026854"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Hozzáférés kezelése SaaS alkalmazásokhoz egy csoport használatával

Az Azure Active Directory (Azure AD) azure AD Premium-licenccsomaggal való használatával csoportok használatával hozzáférést rendelhet az Azure AD-vel integrált SaaS-alkalmazáshoz. Ha például hozzá szeretne rendelni egy hozzáférést a marketingosztályhoz öt különböző SaaS-alkalmazás használatához, létrehozhat egy csoportot, amely tartalmazza a marketingosztály felhasználóit, majd hozzárendelheti a csoportot az öt SaaS-alkalmazáshoz, amelyekre a marketing osztály. Így időt takaríthat meg, ha egy helyen kezeli a marketingosztály tagságát. A felhasználók ezután hozzá lesznek rendelve az alkalmazáshoz, amikor a marketingcsoport tagjaiként hozzáadódnak, és a hozzárendeléseiket eltávolítják az alkalmazásból, amikor eltávolítják őket a marketingcsoportból. Ez a funkció több száz olyan alkalmazással használható, amelyeket az Azure AD alkalmazásgalériából adhat hozzá.

> [!IMPORTANT]
> Ezt a funkciót csak az Azure AD Premium próbaverzió indítása vagy az Azure AD Premium licenccsomag vásárlása után használhatja.
> A csoportalapú hozzárendelés csak biztonsági csoportok esetén támogatott.
> A beágyazott csoporttagság az alkalmazásokhoz történő csoportalapú hozzárendeléseknél egyelőre nem támogatott.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Hozzáférés hozzárendelése egy felhasználóhoz vagy csoporthoz SaaS-alkalmazáshoz

1. Az [Azure AD felügyeleti központban](https://aad.portal.azure.com)válassza a **Nagyvállalati alkalmazások**lehetőséget.
2. Az alkalmazástárból hozzáadott alkalmazás kiválasztásával nyissa meg.
3. Válassza a **Felhasználók és csoportok**lehetőséget, majd a Felhasználó **hozzáadása**lehetőséget.
4. A **Hozzárendelés hozzáadása**csoportban válassza a Felhasználók és **csoportok** lehetőséget a Felhasználók **és csoportok** kijelölési listájának megnyitásához.
6. Jelöljön ki annyi csoportot vagy felhasználót, amennyit csak szeretne, majd a **Kijelölés gombra** vagy a Kijelölés elemre kattintva vegye fel őket a **Hozzárendelés hozzáadása** listába. Ebben a szakaszban szerepkört is hozzárendelhet egy felhasználóhoz.
7. Jelölje be a **Hozzárendelés** jelölőnégyzetet, ha a felhasználókat vagy csoportokat hozzá szeretné rendelni a kijelölt vállalati alkalmazáshoz.

## <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](groups-settings-cmdlets.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
