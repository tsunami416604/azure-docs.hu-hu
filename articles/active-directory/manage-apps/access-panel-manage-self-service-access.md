---
title: Az önkiszolgáló alkalmazások hozzáférése használata | Microsoft dokumentumok
description: Az önkiszolgáló alkalmazások hozzáférésének engedélyezése, hogy a felhasználók megtalálják saját alkalmazásaikat
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55da8731855c8afda496edff33f3fbb7982cd44b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784556"
---
# <a name="how-to-use-self-service-application-access"></a>Az önkiszolgáló alkalmazás-hozzáférés használata

Ahhoz, hogy a felhasználók önfelderíthessék az alkalmazásokat a hozzáférési paneljükről, engedélyeznie kell **az önkiszolgáló alkalmazások hozzáférését** minden olyan alkalmazáshoz, amelyet lehetővé kíván tenni a felhasználók számára, hogy önfelderítsék és hozzáférést kérjenek.

Ez a funkció nagyszerű módja annak, hogy időt és pénzt takarítson meg informatikai csoportként, és erősen ajánlott egy modern alkalmazás-telepítés részeként az Azure Active Directoryval.

Ez a szolgáltatás lehetővé teszi:

-   Lehetővé teszi a felhasználók saját maguk nak felderíteni alkalmazásokat az [alkalmazás hozzáférési panel](https://myapps.microsoft.com/) nélkül zavarják az informatikai csoport.

-   Adja hozzá ezeket a felhasználókat egy előre konfigurált csoporthoz, így láthatja, hogy ki kért hozzáférést, eltávolíthatja a hozzáférést, és kezelheti a hozzájuk rendelt szerepköröket.

-   Tetszés szerint engedélyezheti, hogy egy üzleti jóváhagyó jóváhagyja az alkalmazáshozzáférési kérelmeket, így az informatikai csoportnak nem kell.

-   Tetszés szerint legfeljebb 10 személyt konfigurálhat, akik jóváhagyhatják az alkalmazáshoz való hozzáférést.

-   Ha engedélyezi a vállalati jóváhagyónak, hogy közvetlenül a vállalkozás jóváhagyójának [alkalmazás-hozzáférési paneljéből](https://myapps.microsoft.com/)állítsa be azokat a jelszavakat, amelyekkel a felhasználók bejelentkezhetnek az alkalmazásba.

-   Szükség esetén automatikusan automatikusan hozzárendeli az önkiszolgáló hozzárendelt felhasználókat egy alkalmazásszerepkörhöz közvetlenül.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Az önkiszolgáló alkalmazások hozzáférésének engedélyezése, hogy a felhasználók megtalálják saját alkalmazásaikat

Az önkiszolgáló alkalmazások elérése nagyszerű módja annak, hogy a felhasználók önfelderíthessék az alkalmazásokat, opcionálisan lehetővé teszik az üzleti csoport számára az alkalmazásokhoz való hozzáférés jóváhagyását. Engedélyezheti, hogy az üzleti csoport közvetlenül a hozzáférési paneljeikről kezelje a jelszóegyszeri bejelentkezésalkalmazások hoz a felhasználókhoz rendelt hitelesítő adatokat.

Ha engedélyezni szeretné az önkiszolgáló alkalmazások hozzáférését egy alkalmazáshoz, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az **Enterprise Applications** elemre az Azure Active Directory bal oldali navigációs menüjében.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

   * Ha nem látja a kívánt alkalmazást, használja a **Szűrő** vezérlőt a **Minden alkalmazás lista** tetején, és állítsa a **Megjelenítés** beállítást **minden alkalmazásra.**

6. Válassza ki a listából azt az alkalmazást, amelyhez engedélyezni szeretné az önkiszolgáló hozzáférést.

7. Miután az alkalmazás betöltődik, kattintson az **Önkiszolgáló** az alkalmazás bal oldali navigációs menüjében.

8. Ha engedélyezni szeretné az önkiszolgáló alkalmazások hozzáférését ehhez az alkalmazáshoz, kapcsolja **Yes.** be a **Felhasználók hozzáférésének engedélyezése az alkalmazáshoz?**

9. Ezután válassza ki azt a csoportot, amelyhez az alkalmazáshoz hozzáférést kérő felhasználókat hozzá kell adni, kattintson a címke melletti **választóra, amelyhez a hozzárendelendő felhasználókhoz kell rendelni?**

10. **Nem kötelező:** Ha üzleti jóváhagyást szeretne igényelni a felhasználók hozzáférésének engedélyezéséhez, állítsa be a Jóváhagyás **Yes** **megkövetelése lehetőséget az alkalmazáshoz való hozzáférés megadása előtt?**

11. **Nem kötelező: Ha csak egyszer jelentkező jelszóval rendelkező alkalmazások esetén engedélyezi,** hogy ezek az üzleti jóváhagyók megadják az alkalmazásnak a jóváhagyott felhasználók számára **Yes**küldött jelszavakat, állítsa be a **Jóváhagyók számára a felhasználó jelszavait az alkalmazáshoz?**

12. **Nem kötelező:** Az alkalmazáshoz való hozzáférés jóváhagyására engedélyezett üzleti jóváhagyók megadásához kattintson a címke melletti **választóra, aki jóváhagyhatja az alkalmazáshoz való hozzáférést?**

    * A csoportok nem támogatottak.

13. **Nem kötelező:** **A szerepköröket elérhető alkalmazások esetében,** ha önkiszolgálójóváhagyott felhasználókat kíván hozzárendelni egy szerepkörhöz, kattintson a Címzett szerepkör melletti választóra **az alkalmazásban?**

14. A befejezéshez kattintson a fűrészlap tetején található **Mentés** gombra.

Az önkiszolgáló alkalmazások konfigurálásának befejezése után a felhasználók az [alkalmazás-hozzáférési panelre](https://myapps.microsoft.com/) navigálhatnak, és a **+Hozzáadás** gombra kattintva megkereshetik azokat az alkalmazásokat, amelyekhez engedélyezte az önkiszolgáló hozzáférést. A vállalati jóváhagyók az [alkalmazáshoz való hozzáférés panelen](https://myapps.microsoft.com/)is megjelennek egy értesítés. Engedélyezheti, hogy egy e-mailt értesítsen őket, ha a felhasználó hozzáférést kért egy olyan alkalmazáshoz, amely jóváhagyást igényel. 

Ezek a jóváhagyások csak az egyszeri jóváhagyási munkafolyamatokat támogatják, ami azt jelenti, hogy ha több jóváhagyót ad meg, bármely jóváhagyó jóváhagyhatja az alkalmazáshoz való hozzáférést.

## <a name="next-steps"></a>További lépések
[Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre](../users-groups-roles/groups-self-service-management.md)
