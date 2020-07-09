---
title: Nem megfelelő felhasználók vannak kiépítve egy Azure AD Gallery-alkalmazásba
description: Ismerje meg, hogy miként állapítható meg, hogy egy másik felhasználó hogyan legyen kiépítve egy alkalmazásba, mint amennyit várt
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/20/2018
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 5a109f1a06e7ee8aff3e455c009217ff670d2781
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782211"
---
# <a name="wrong-set-of-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Helytelenek a felhasználók egy Azure AD Gallery-alkalmazásban való üzembe helyezése

Az alkalmazáshoz kiépített felhasználók elsődlegesen a felhasználók és a csoportok az alkalmazáshoz vannak **rendelve** .

Az alábbi forrásokból megtudhatja, hogyan ellenőrizhető, hogy mely felhasználók és csoportok vannak hozzárendelve egy alkalmazáshoz Azure Active Directoryon belül.

## <a name="assign-a-user-directly-as-an-administrator"></a>Felhasználó közvetlen kijelölése rendszergazdaként

Ha egy vagy több felhasználót közvetlenül szeretne hozzárendelni egy alkalmazáshoz, kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amelyhez felhasználót szeretne hozzárendelni a listából.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. A **hozzárendelés hozzáadása** ablaktábla megnyitásához kattintson a **Hozzáadás** gombra a **felhasználók és csoportok** lista tetején.

9. a **hozzárendelés hozzáadása** panelen kattintson a **felhasználók és csoportok** választóra.

10. Írja be annak a felhasználónak a **teljes nevét** vagy **e-mail-címét** , **akit a keresés név vagy e-mail cím keresőmező alapján** szeretne hozzárendelni.

11. A **jelölőnégyzet**megjelenítéséhez vigye a kurzort a listában szereplő **felhasználó** fölé. Kattintson a felhasználó profiljának fényképe vagy emblémája melletti jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

12. Nem **kötelező:** Ha **egynél több felhasználót szeretne felvenni**, írjon be egy másik **teljes nevet** vagy **e-mail-címet** a **Keresés név vagy e-mail-cím** Keresés mezőbe, és kattintson a jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

13. Ha befejezte a felhasználók kiválasztását, kattintson a **kiválasztás** gombra, és adja hozzá őket az alkalmazáshoz hozzárendelni kívánt felhasználók és csoportok listájához.

14. Nem **kötelező:** kattintson a **szerepkör** kiválasztása lehetőségre a **hozzárendelés hozzáadása** panelen, és válassza ki a kiválasztott felhasználókhoz hozzárendelni kívánt szerepkört.

15. Kattintson a **hozzárendelés** gombra az alkalmazás a kijelölt felhasználókhoz való hozzárendeléséhez.

Ha a kiépítés konfigurálva van, és már fut egy alkalmazáshoz, az új felhasználókat körülbelül 10 perc alatt kell kiépíteni egy alkalmazásba. A részletekért ellenőrizze a **naplókat** .

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Csoport kijelölése közvetlenül egy alkalmazáshoz rendszergazdaként

Ha egy vagy több csoportot szeretne közvetlenül egy alkalmazáshoz rendelni, kövesse az alábbi lépéseket:

1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** elemre kattintva.

3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.

4. kattintson a **vállalati alkalmazások** lehetőségre a bal oldali navigációs menü Azure Active Directory.

5. kattintson a **minden alkalmazás** elemre az alkalmazások listájának megtekintéséhez.

   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**

6. Válassza ki azt az alkalmazást, amelyhez felhasználót szeretne hozzárendelni a listából.

7. Az alkalmazás betöltése után kattintson a **felhasználók és csoportok** elemre az alkalmazás bal oldali navigációs menüjében.

8. A **hozzárendelés hozzáadása** ablaktábla megnyitásához kattintson a **Hozzáadás** gombra a **felhasználók és csoportok** lista tetején.

9. a **hozzárendelés hozzáadása** panelen kattintson a **felhasználók és csoportok** választóra.

10. Írja be annak a csoportnak a **teljes csoportjának nevét** , amelyet szeretne a **Keresés név vagy e-mail-cím** keresése mezőbe rendelni.

11. A **jelölőnégyzet**megjelenítéséhez vigye a kurzort a listában a **csoport** fölé. Kattintson a csoport profiljának fényképe vagy emblémája melletti jelölőnégyzetre a felhasználó a **kiválasztott** listához való hozzáadásához.

12. Nem **kötelező:** Ha egynél **több csoportot szeretne felvenni**, írjon be egy másik **teljes nevet** a **Keresés név vagy e-mail-cím** keresőmezőbe, majd a jelölőnégyzetre kattintva vegye fel a csoportot a **kiválasztott** listára.

13. Ha befejezte a csoportok kijelölését, kattintson a **kiválasztás** gombra, és adja hozzá őket az alkalmazáshoz hozzárendelni kívánt felhasználók és csoportok listájához.

14. Nem **kötelező:** kattintson a **szerepkör** kiválasztása lehetőségre a **hozzárendelés hozzáadása** panelen, és válassza ki a kiválasztott csoportokhoz hozzárendelni kívánt szerepkört.

15. Kattintson a **hozzárendelés** gombra az alkalmazás a kijelölt csoportokhoz való hozzárendeléséhez.

Ha a kiépítés konfigurálva van, és már fut egy alkalmazáshoz, a csoporton belül található új felhasználókat körülbelül 10 perc alatt kell kiépíteni egy alkalmazásba. A részletekért ellenőrizze a **naplókat** .

>[!IMPORTANT]
>A csoport neve és a csoport adatainak kiépítés a tagokon kívül, ha egyes alkalmazások esetében támogatott. A funkció engedélyezéséhez vagy letiltásához engedélyezze vagy tiltsa le a **kiépítés** lapon megjelenő csoportosítási objektumok **leképezését** . 
>
>

Ha a létesítési csoportok engedélyezve vannak, tekintse át az attribútumok leképezéseit, és győződjön meg arról, hogy a megfelelő mező használatban van a "megfeleltetési azonosító" beállításnál. Ez a megfeleltetési azonosító lehet a megjelenítendő név vagy az e-mail-alias. A csoport és a tagjai nincsenek kiépítve, ha a megfelelő tulajdonság üres vagy nincs feltöltve egy csoportra az Azure AD-ben.

## <a name="next-steps"></a>További lépések
[A felhasználók üzembe helyezésének automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory](user-provisioning.md)
