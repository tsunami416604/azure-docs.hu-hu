---
title: Probléma az önkiszolgáló alkalmazások hozzáférése használata | Microsoft dokumentumok
description: Önkiszolgáló alkalmazás-hozzáféréssel kapcsolatos problémák elhárítása
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
ms.openlocfilehash: a981dfb1d72c21eccf2ad7119ea219114ed15aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784278"
---
# <a name="problem-using-self-service-application-access"></a>Probléma az önkiszolgáló alkalmazások hozzáférése

Az önkiszolgáló alkalmazások elérése nagyszerű módja annak, hogy a felhasználók önfelderíthessék az alkalmazásokat, opcionálisan lehetővé teszik az üzleti csoport számára az alkalmazásokhoz való hozzáférés jóváhagyását. Engedélyezheti, hogy az üzleti csoport közvetlenül a hozzáférési paneljeikről kezelje a jelszóegyszeri bejelentkezésalkalmazások hoz a felhasználókhoz rendelt hitelesítő adatokat.

Ahhoz, hogy a felhasználók önfelderíthessék az alkalmazásokat a hozzáférési paneljükről, engedélyeznie kell **az önkiszolgáló alkalmazások hozzáférését** minden olyan alkalmazáshoz, amelyet lehetővé kíván tenni a felhasználók számára, hogy önfelderítsék és hozzáférést kérjenek.

## <a name="general-issues-to-check-first"></a>Általános kérdések, amelyeket először ellenőrizni kell

-   Győződjön meg arról, hogy az önkiszolgáló alkalmazás-hozzáférés megfelelően van konfigurálva. Lásd" Az önkiszolgáló alkalmazás-hozzáférés konfigurálása.

-   Győződjön meg arról, hogy a felhasználó vagy a csoport engedélyezve van az önkiszolgáló alkalmazások elérésének kérelmezéséhez.

-   Győződjön meg arról, hogy a felhasználó az önkiszolgáló alkalmazások eléréséhez megfelelő helyre látogat. a felhasználók megkereshetik [az alkalmazás-hozzáférési panelt,](https://myapps.microsoft.com/) és a **+Hozzáadás** gombra kattintva megkereshetik azokat az alkalmazásokat, amelyekhez önkiszolgáló hozzáférést engedélyezett.

-   Ha az önkiszolgáló alkalmazás-hozzáférés csak nemrég lett konfigurálva, próbáljon meg bejelentkezni és kijelentkezni a felhasználó hozzáférési paneljén néhány perc múlva, hogy lássa, megjelentek-e az önkiszolgáló hozzáférési módosítások.

## <a name="how-to-configure-self-service-application-access"></a>Az önkiszolgáló alkalmazás-hozzáférés konfigurálása

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

    >[!NOTE]
    > A csoportok nem támogatottak.
    >
    >

13. **Nem kötelező:** **A szerepköröket elérhető alkalmazások esetében,** ha önkiszolgálójóváhagyott felhasználókat kíván hozzárendelni egy szerepkörhöz, kattintson a Címzett szerepkör melletti választóra **az alkalmazásban?**

14. A befejezéshez kattintson a fűrészlap tetején található **Mentés** gombra.

Az önkiszolgáló alkalmazások konfigurálásának befejezése után a felhasználók az [alkalmazás-hozzáférési panelre](https://myapps.microsoft.com/) navigálhatnak, és a **+Hozzáadás** gombra kattintva megkereshetik azokat az alkalmazásokat, amelyekhez engedélyezte az önkiszolgáló hozzáférést. A vállalati jóváhagyók az [alkalmazáshoz való hozzáférés panelen](https://myapps.microsoft.com/)is megjelennek egy értesítés. Engedélyezheti, hogy egy e-mailt értesítsen őket, ha a felhasználó hozzáférést kért egy olyan alkalmazáshoz, amely jóváhagyást igényel. 

Ezek a jóváhagyások csak az egyszeri jóváhagyási munkafolyamatokat támogatják, ami azt jelenti, hogy ha több jóváhagyót ad meg, bármely jóváhagyó jóváhagyhatja az alkalmazáshoz való hozzáférést.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépések nem oldják meg a problémát 

nyisson meg egy támogatási jegyet a következő információkkal, ha azok rendelkezésre állnak:

-   Korrelációs hiba azonosítója

-   UPN (felhasználói e-mail cím)

-   TenantID

-   Böngésző típusa

-   Időzóna és idő/időkeret hiba esetén

-   Hegedűs nyomok

## <a name="next-steps"></a>További lépések
[Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre](../users-groups-roles/groups-self-service-management.md)
