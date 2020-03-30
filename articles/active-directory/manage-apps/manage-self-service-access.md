---
title: Az önkiszolgáló alkalmazás-hozzárendelés konfigurálása | Microsoft dokumentumok
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
ms.date: 10/23/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcccc64e0c766164a06932e9b65a4459816f9deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409116"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Az önkiszolgáló alkalmazás-hozzárendelés konfigurálása

Ahhoz, hogy a felhasználók saját maguk fedezhessék fel az alkalmazásokat a Saját alkalmazások hozzáférési paneljükről, engedélyeznie kell **az önkiszolgáló alkalmazások hozzáférését** minden olyan alkalmazáshoz, amelyet lehetővé kíván tenni a felhasználók számára, hogy önfelderítsék és hozzáférést kérjenek. Ez a funkció az [Azure AD-galériából](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app), az [Azure AD alkalmazásproxyból](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) hozzáadott vagy [a felhasználó vagy a rendszergazda hozzájárulásával](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)hozzáadott alkalmazások számára érhető el. 

Ez a funkció nagyszerű módja annak, hogy időt és pénzt takarítson meg informatikai csoportként, és erősen ajánlott egy modern alkalmazás-telepítés részeként az Azure Active Directoryval.

Ez a szolgáltatás lehetővé teszi:

-   Lehetővé teszi a felhasználók számára, hogy saját maguk fedezhessék fel az alkalmazásokat a [Saját alkalmazások hozzáférési panelről](https://myapps.microsoft.com/) anélkül, hogy zavarnák az informatikai csoportot.

-   Adja hozzá ezeket a felhasználókat egy előre konfigurált csoporthoz, így láthatja, hogy ki kért hozzáférést, eltávolíthatja a hozzáférést, és kezelheti a hozzájuk rendelt szerepköröket.

-   Tetszés szerint engedélyezheti, hogy egy üzleti jóváhagyó jóváhagyja az alkalmazáshozzáférési kérelmeket, így az informatikai csoportnak nem kell.

-   Tetszés szerint legfeljebb 10 személyt konfigurálhat, akik jóváhagyhatják az alkalmazáshoz való hozzáférést.

-   Ha engedélyezi a vállalati jóváhagyónak, hogy közvetlenül a vállalkozás jóváhagyójának [alkalmazás-hozzáférési paneljéből](https://myapps.microsoft.com/)állítsa be azokat a jelszavakat, amelyekkel a felhasználók bejelentkezhetnek az alkalmazásba.

-   Szükség esetén automatikusan automatikusan hozzárendeli az önkiszolgáló hozzárendelt felhasználókat egy alkalmazásszerepkörhöz közvetlenül.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Az önkiszolgáló alkalmazások hozzáférésének engedélyezése, hogy a felhasználók megtalálják saját alkalmazásaikat

Az önkiszolgáló alkalmazások elérése nagyszerű módja annak, hogy a felhasználók önfelderíthessék az alkalmazásokat, és adott esetben lehetővé tegyék az üzleti csoport számára az alkalmazásokhoz való hozzáférés jóváhagyását. Az alkalmazásokon egyszeri jellel rendelkező jelszó esetén engedélyezheti az üzleti csoportszámára a felhasználókhoz rendelt hitelesítő adatok kezelését a saját Saját alkalmazások hozzáférési paneljeikről.

Ha engedélyezni szeretné az önkiszolgáló alkalmazások hozzáférését egy alkalmazáshoz, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) globális rendszergazdaként.

2. Válassza az **Azure Active Directory**lehetőséget. A bal oldali navigációs menüben válassza a **Vállalati alkalmazások lehetőséget.**

3. Válassza ki az alkalmazást a listából. Ha nem látja az alkalmazást, írja be a nevét a keresőmezőbe. Vagy használja a szűrővezérlőket az alkalmazás típusának, állapotának vagy láthatóságának kiválasztásához, majd válassza **az Alkalmaz**lehetőséget.

4. A bal oldali navigációs menüben válassza az **Önkiszolgáló**lehetőséget.

5. Ha engedélyezni szeretné az önkiszolgáló alkalmazások hozzáférését ehhez az alkalmazáshoz, kapcsolja **Yes.** be a **Felhasználók hozzáférésének engedélyezése az alkalmazáshoz?**

6. A **Hozzá rendelt felhasználók hoz**lehetőséget választva kattintson a **Csoport kiválasztása**gombra. Válasszon egy csoportot, majd kattintson **a Kijelölés gombra.** Ha egy felhasználó kérelmét jóváhagyják, a rendszer hozzáadja őket ehhez a csoporthoz. A csoport tagságának megtekintésekor láthatja, hogy önkiszolgáló hozzáféréssel rendelkezik-e az alkalmazáshoz.
  
    > [!NOTE]
    > Ez a beállítás nem támogatja a helyszíni szinkronizálású csoportokat.

7. **Nem kötelező:** Ha a felhasználók hozzáférésének engedélyezéséhez üzleti jóváhagyást szeretne igényelni, állítsa be **Yes**a **Jóváhagyás megkövetelése az alkalmazáshoz való hozzáférés megadása előtt?**

8. **Nem kötelező: Ha csak egyszer jelű jelszóval használják a jelszót,** hogy az üzleti jóváhagyók megadhassák az alkalmazásnak a jóváhagyott felhasználók **Yes**számára küldött jelszavakat, állítsa be a **Jóváhagyók számára a felhasználó jelszavait az alkalmazáshoz?**

9. **Nem kötelező:** Az alkalmazáshoz való hozzáférés jóváhagyására engedélyezett üzleti jóváhagyók megadásához a **Ki hagyhatja jóvá az alkalmazáshoz való hozzáférést?** gomb, **kattintson a Jóváhagyók kiválasztása**parancsra, majd válasszon ki legfeljebb 10 egyéni üzleti jóváhagyót. Ezután kattintson a **Kiválasztás** elemre.

    >[!NOTE]
    >A csoportok nem támogatottak. Legfeljebb 10 egyéni üzleti jóváhagyó t választhat ki. Ha több jóváhagyót ad meg, bármely jóváhagyó jóváhagyhat egy hozzáférési kérelmet.

10. **Nem kötelező:** **A szerepköröket tartalmazó alkalmazások esetén**az önkiszolgálójóváhagyott felhasználókszerepkörhöz rendeléséhez a Felhasználók számára melyik szerepkör t rendelje hozzá ebben az **alkalmazásban?**, kattintson a **Szerepkör kiválasztása**gombra, majd válassza ki azt a szerepkört, amelyhez ezeket a felhasználókat hozzá kell rendelni. Ezután kattintson a **Kiválasztás** elemre.

11. A befejezéshez kattintson az ablaktábla tetején található **Mentés** gombra.

Az önkiszolgáló alkalmazások konfigurálásának befejezése után a felhasználók a [Saját alkalmazások hozzáférési panelre](https://myapps.microsoft.com/) navigálhatnak, és az **önkiszolgáló alkalmazások hozzáadása** gombra kattintva megkereshetik azokat az alkalmazásokat, amelyek önkiszolgáló hozzáféréssel rendelkeznek. Az üzleti jóváhagyók a [Saját alkalmazások hozzáférési panelen](https://myapps.microsoft.com/)is megjelennek egy értesítés. Engedélyezheti, hogy egy e-mailt értesítsen őket, ha a felhasználó hozzáférést kért egy olyan alkalmazáshoz, amely jóváhagyást igényel.

## <a name="next-steps"></a>További lépések
[Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre](../users-groups-roles/groups-self-service-management.md)
