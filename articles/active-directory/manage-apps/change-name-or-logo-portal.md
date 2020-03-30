---
title: Vállalati alkalmazás nevének vagy emblémájának módosítása az Azure AD-ben
description: Egyéni vállalati alkalmazások nevének vagy emblémájának módosítása az Azure Active Directoryban
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138501"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Vállalati alkalmazás nevének vagy emblémájának módosítása az Azure Active Directoryban

Az Azure Active Directoryban (Azure AD) egyszerűen módosíthatja egy egyéni vállalati alkalmazás nevét vagy emblémáját. A módosítások végrehajtásához megfelelő engedélyekkel kell rendelkeznie, és önnek kell az egyéni alkalmazás létrehozójának lennie.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Hogyan módosíthatom egy vállalati alkalmazás nevét vagy emblémáját?

1. Jelentkezzen be az [Azure Active Directory-portálra](https://aad.portal.azure.com/) egy olyan fiókkal, amely a címtár globális rendszergazdája. Megjelenik **az Azure Active Directory felügyeleti központ** lapja.
2. A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet. Megjelenik a vállalati alkalmazások listája.
3. Válasszon ki egy alkalmazást. Megjelenik az alkalmazás áttekintő lapja.
4. Az alkalmazás áttekintő ablaktábláján a **Kezelés** fejlécben válassza a **Tulajdonságok lehetőséget.** Megjelenik **a Tulajdonságok** lap.
5. Ha módosítani szeretné a nevet, jelölje be a **Név** mezőt, írja be az új nevet, és nyomja le az **Enter billentyűt.**
6. Ha módosítani szeretné az emblémát, keresse meg az **Embléma** mezőt, és jelölje ki a Mappa ikonját a **Fájl kiválasztása** mező mellett, amely az alkalmazás aktuális emblémaképe alatt található.

   ![A tulajdonságok parancs kijelölése](./media/change-name-or-logo-portal/change-logo.png)

   Ellenkező esetben, ha nem módosítja az emblémát, folytassa a 8.
7. A fájlválasztóban jelölje ki a kívánt fájlt új emblémaként. A fájl neve az aktuális emblémakép alatti mezőben jelenik meg.

   > [!NOTE]
   > Az Azure megköveteli, hogy az emblémakép PNG-fájl legyen, és korlátozza a szélességet, a magasságot és a fájlméretet. Az egyéni emblémáknak pontosan 215 215 &times; képpont méretűnek és PNG formátumúnak kell lenniük. Azt javasoljuk, hogy használjon egyszínű hátteret, amelynek nincs átlátszósága az alkalmazás emblémájában, hogy a felhasználók számára a legjobban jelenjen meg.
8. Kattintson a **Mentés** gombra. Ha új emblémát választott, az **Embléma** mező képe az új emblémafájlnak megfelelően változik.

## <a name="next-steps"></a>További lépések

* [Rövid útmutató: A vállalat csoportjainak és csoporttagjainak megtekintése az Azure Active Directoryban](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz](assign-user-or-group-access-portal.md)
* [Felhasználó- vagy csoporthozzárendelés eltávolítása vállalati alkalmazásból](remove-user-or-group-access-portal.md)
* [Vállalati alkalmazás felhasználói bejelentkezésének letiltása](disable-user-sign-in-portal.md)
