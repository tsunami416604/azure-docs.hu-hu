---
title: Név vagy az Azure Active Directoryban egy vállalati alkalmazás emblémájának módosítása |} A Microsoft Docs
description: A név vagy egy egyéni vállalati alkalmazást az Azure Active Directory emblémája módosítása
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
ms.openlocfilehash: 30da8d6843c27c42d4d99adef50b9ad98a131c95
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780923"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Név vagy az Azure Active Directoryban egy vállalati alkalmazás emblémájának módosítása

Módosítsa a nevét vagy az Azure Active Directoryban (Azure AD) egy egyéni vállalati alkalmazás emblémája, könnyebbé vált. A módosítások a megfelelő engedélyekkel kell rendelkeznie, és az egyéni alkalmazás készítőjét, kell lennie.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Hogyan változtatható meg egy vállalati alkalmazás nevére vagy emblémájára?

1. Jelentkezzen be a [Azure Active Directory portálon](https://aad.portal.azure.com/) egy olyan fiókkal, amely a címtár globális rendszergazdája. A **Azure Active Directory felügyeleti központ** lap jelenik meg.
2. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet. A vállalati alkalmazások listája jelenik meg.
3. Válasszon ki egy alkalmazást. Az alkalmazás áttekintőlapján jelenik meg.
4. Az alkalmazás áttekintése panelen alatt a **kezelés** szakaszban kattintson **tulajdonságok**. A **tulajdonságok** lap jelenik meg.
5. Ha azt szeretné, módosítsa a nevet, válassza ki a **neve** be, írja be az új nevet, és nyomja meg **Enter**.
6. Ha szeretné módosítani az embléma, keresse meg a **embléma** mezőben, majd válassza ki a mappaikont a a **válasszon ki egy fájlt** mezőbe, amely az alkalmazás aktuális embléma kép alatt.

   ![A Tulajdonságok parancs kiválasztása](./media/change-name-or-logo-portal/change-logo.png)

   Ha az embléma nem változnak, ellenkező esetben nyissa meg a 8. lépés.
7. A Fájlkereső válassza a kívánt fájlt, az új emblémát. A fájl nevét a mezőbe az aktuális embléma kép alatt jelenik meg.

   > [!NOTE]
   > Azure megköveteli a emblémakép PNG-fájl, és a szélességét, a magasságra és a fájlméret korlátok vonatkozik.
8. Kattintson a **Mentés** gombra. Ha úgy döntött, hogy egy új embléma a **embléma** mező kép módosításai tükrözik az új embléma fájlt.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: A munkahelyi csoportok és tagok megtekintése az Azure Active Directoryban](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](assign-user-or-group-access-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazás](remove-user-or-group-access-portal.md)
* [Tiltsa le a felhasználók bejelentkezési folyamatába egy vállalati alkalmazás](disable-user-sign-in-portal.md)
