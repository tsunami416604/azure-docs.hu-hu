---
title: "Dedikált csoportok az Azure Active Directoryban |} Microsoft Docs"
description: "Hogyan dedikált csoportok áttekintése az Azure Active Directory és a létrehozott hogyan működnek."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 86158909-083a-41fe-8090-955e96ad1865
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;oldportal
ms.openlocfilehash: 992f4563064d7a292cf4fdd90a9a3c84cdec91c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="dedicated-groups-in-azure-active-directory"></a>Dedikált csoportok az Azure Active Directoryban
Az Azure Active Directory (Azure AD) a dedikált csoportok funkció automatikusan létrehozza és tölti fel az előre definiált az Azure AD-csoportok tagságát. Dedikált csoportok tagjai nem adható hozzá, vagy a klasszikus Azure portálon, a Windows PowerShell-parancsmagok segítségével távolítja el vagy programon keresztül.

> [!NOTE]
> Dedikált csoportok szükséges, hogy a van rendelve egy Azure AD Premium-licenc
>
> * A szabály a csoporton felügyelő rendszergazda
> * a felhasználók, akik ki van jelölve a szabály a csoport tagjai
>
>

**Dedikált csoportok engedélyezése**

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be **Active Directory**, majd nyissa meg a munkahely címtárában.
2. Válassza ki a **csoportok** lapra, és nyissa meg a szerkeszteni kívánt csoportot.
3. Válassza ki a **konfigurálása** lapot, és utána állítsa be **dedikált csoportok engedélyezése** való **Igen**.

Amennyiben a dedikált csoportok engedélyezése kapcsoló értéke **Igen**, további engedélyezheti a könyvtárat úgy, hogy az összes felhasználó dedikált csoport automatikus létrehozásához a **az "Összes felhasználó" csoport** váltani **Igen**. Is szerkesztheti a kijelölt csoport nevét írja be azt a **"Minden felhasználó" megjelenített név csoport** mező.

A minden felhasználó csoportban a azonos engedélyek hozzárendelése a címtárban szereplő összes felhasználó használható. Például biztosíthat minden felhasználó a könyvtár hozzáférés SaaS-alkalmazás az összes felhasználó dedikált csoport hozzáférést rendel az alkalmazás.

A dedikált minden felhasználó csoportban a címtárban, beleértve a vendégek és a külső felhasználók felhasználókat tartalmazza. Ha egy csoportot kell, amely nem tartalmazza a külső felhasználók számára, akkor ez által csoportot hoz létre egy attribútum-alapú dinamikus szabály például a következő érhető el:

                (user.userPrincipalName -notContains "#EXT#@")

Egy csoport, amely nem tartalmazza az összes vendégek például a következő szabályt használni:

                (user.userType -ne "Guest")

Információk a dinamikus csoporttagsághoz kapcsolódó *speciális* (akár többszörös összehasonlítást is tartalmazó) szabályok létrehozásáról: [Using attributes to create advanced rules](active-directory-accessmanagement-groups-with-advanced-rules.md) (Attribútumok használata speciális szabályok létrehozásához).

### <a name="next-steps"></a>Következő lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Mi az az Azure Active Directory?](active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
