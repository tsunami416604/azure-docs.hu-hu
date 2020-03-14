---
title: Vállalati alkalmazás nevének vagy emblémájának módosítása az Azure AD-ben
description: Egyéni vállalati alkalmazás nevének vagy emblémájának módosítása Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d23849df75d1ab239eb269b462abb21df196e7e5
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138501"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Vállalati alkalmazás nevének vagy emblémájának módosítása Azure Active Directory

A Azure Active Directory (Azure AD) alkalmazásban egyszerűen módosíthatja az egyéni vállalati alkalmazások nevét vagy emblémáját. A módosítások elvégzéséhez megfelelő engedélyekkel kell rendelkeznie, és az egyéni alkalmazás létrehozójának kell lennie.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Hogyan módosíthatja a vállalati alkalmazások nevét vagy emblémáját?

1. Jelentkezzen be a [Azure Active Directory portálra](https://aad.portal.azure.com/) egy olyan fiókkal, amely a címtár globális rendszergazdája. Megjelenik a **Azure Active Directory felügyeleti központ** lap.
2. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet. Megjelenik a vállalati alkalmazások listája.
3. Válasszon egy alkalmazást. Megjelenik az alkalmazás áttekintése oldal.
4. Az alkalmazás áttekintése ablaktáblán, a **kezelés** fejléc alatt válassza a **Tulajdonságok**elemet. Megjelenik a **Tulajdonságok** lap.
5. Ha módosítani szeretné a nevet, jelölje be a **név** mezőt, írja be az új nevet, majd nyomja le az **ENTER**billentyűt.
6. Ha módosítani kívánja az emblémát, keresse meg az **embléma** mezőt, és válassza a **fájl kiválasztása** mező melletti mappa ikont, amely az alkalmazás aktuális emblémájának képe alá esik.

   ![A Properties parancs kiválasztása](./media/change-name-or-logo-portal/change-logo.png)

   Ellenkező esetben, ha nem módosítja az emblémát, ugorjon a 8. lépésre.
7. A file Picker (fájl kiválasztása) területen válassza ki az új emblémaként használni kívánt fájlt. A fájl neve az aktuális embléma képe alatti mezőben jelenik meg.

   > [!NOTE]
   > Az Azure-ban a logónak PNG-fájlként kell lennie, és a szélességre, a magasságra és a fájlméretre vonatkozó korlátozásokat alkalmaz. Az egyéni emblémának pontosan 215 &times; 215 képpont méretűnek kell lennie, és PNG formátumban kell lennie. Javasoljuk, hogy az alkalmazás emblémájában ne jelenjen meg átlátszóság, ha a felhasználók számára a legjobbat szeretné használni.
8. Kattintson a **Mentés** gombra. Ha új emblémát választ, az **embléma** mező képe úgy változik, hogy tükrözze az új embléma fájlját.

## <a name="next-steps"></a>Következő lépések

* [Rövid útmutató: a szervezet csoportjainak és tagjainak megtekintése Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
* [Felhasználó vagy csoport hozzárendelésének eltávolítása vállalati alkalmazásból](remove-user-or-group-access-portal.md)
* [Vállalati alkalmazás felhasználói bejelentkezésének letiltása](disable-user-sign-in-portal.md)
