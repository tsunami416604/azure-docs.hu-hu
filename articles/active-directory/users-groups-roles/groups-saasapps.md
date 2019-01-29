---
title: Egy csoport használatával SaaS-alkalmazásokhoz való hozzáférés kezelése |} A Microsoft Docs
description: Hogyan használható az Azure Active Directory Premium vagy alapszintű csoportok hozzáférések hozzárendelése az Azure Active Directoryval integrált SaaS-alkalmazásokhoz.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/14/2017
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: a7e4a20905b390945c22557af044cf739bc17ce8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197211"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Hozzáférés kezelése SaaS alkalmazásokhoz egy csoport használatával
Azure Active Directory (Azure AD) használatával egy Azure AD prémium vagy alapszintű Azure AD-licenccel rendelkező, használhatja csoportok hozzáférés hozzárendelése egy Azure AD-vel integrált SaaS-alkalmazáshoz. Például, ha szeretné a marketingosztály öt különböző SaaS-alkalmazásokhoz használandó a hozzáférést, is hozzon létre egy csoportot, amely tartalmazza a marketingrészleg számára, és hozzárendelheti ezeket öt SaaS-alkalmazásokhoz, amelyek szükségesek az adott csoport a a marketingosztály. Ezzel a módszerrel kezelése egy helyen a marketingosztály tagsága használatával időt takaríthat meg. Felhasználók majd vannak rendelve az alkalmazás a marketing csoport tagjai kerül, és azok hozzárendeléseiről eltávolította az alkalmazásból a marketing csoport eltávolításakor. Ez a képesség, amely adhat hozzá az Azure AD Alkalmazásgyűjteményben alkalmazások száz is használható.

> [!IMPORTANT]
> Használhatja a funkció csak az Azure AD Premium próbaverziójának elindítása vagy Azure AD prémium vagy alapszintű Azure AD licencet vásárol után. Csoportalapú csak biztonsági csoportok esetében támogatott. A beágyazott csoporttagság az alkalmazásokhoz történő csoportalapú hozzárendeléseknél egyelőre nem támogatott.

**Hozzáférés egy felhasználó vagy csoport hozzárendelése egy SaaS-alkalmazáshoz**

1. Az a [Azure AD felügyeleti központ](https://aad.portal.azure.com)válassza **vállalati alkalmazások**.
2. Jelöljön ki egy alkalmazást, hogy hozzáadta-e, nyissa meg az alkalmazáskatalógusból.
3. Válassza ki **felhasználók és csoportok**, majd válassza ki **felhasználó hozzáadása**.
4. A **hozzárendelés hozzáadása**válassza **felhasználók és csoportok** megnyitásához a **felhasználók és csoportok** választási lista.
6. Tetszőleges számú csoportok kiválasztása vagy a felhasználók a kívánt, majd kattintson vagy koppintson a **kiválasztása** adja hozzá őket a **hozzárendelés hozzáadása** listája. Ebben a szakaszban egy felhasználóra is hozzárendelhet szerepkört.
7. Válassza ki **hozzárendelése** azon felhasználók vagy csoportok hozzárendelése a kiválasztott vállalati alkalmazáshoz.

### <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](groups-settings-cmdlets.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
