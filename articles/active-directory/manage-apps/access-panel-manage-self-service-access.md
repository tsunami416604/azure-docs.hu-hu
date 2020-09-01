---
title: Az önkiszolgáló alkalmazás-hozzáférés használata az Azure AD-ben
description: Önkiszolgáló engedélyezése, hogy a felhasználók megtalálják az alkalmazásokat az Azure AD-ben
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: japere,asteen
ms.openlocfilehash: c789a5fdd2aed7a904dc71065340b3c921ac9abb
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146606"
---
# <a name="how-to-use-self-service-application-access"></a>Önkiszolgáló alkalmazás-hozzáférés használata

Ahhoz, hogy a felhasználók maguk is felfedezzék az alkalmazásokat a saját alkalmazások oldalról, engedélyeznie kell az **önkiszolgáló alkalmazásokhoz való hozzáférést** minden olyan alkalmazáshoz, amelyhez engedélyezni szeretné a felhasználók számára az önfelderítést és a hozzáférés kérését.

Ez a funkció nagyszerű lehetőséget nyújt arra, hogy időt és pénzt takarítson meg informatikai csoportként, és a Azure Active Directory használatával korszerű alkalmazások központi telepítésének részeként erősen ajánlott.

Ha szeretne többet megtudni az alkalmazások végfelhasználói perspektívából való használatáról, tekintse meg [az alkalmazások portál súgóját](../user-help/my-apps-portal-end-user-access.md).

Ez a szolgáltatás lehetővé teszi:

-   Lehetővé teszi a felhasználók számára, hogy az IT-csoport kellemetlensége nélkül is felfedezzék az alkalmazásokat az [alkalmazás-hozzáférési panelről](https://myapps.microsoft.com/) .
-   Adja hozzá ezeket a felhasználókat egy előre konfigurált csoporthoz, így megtekintheti, hogy ki igényelt hozzáférést, távolítsa el a hozzáférést, és kezelje a hozzájuk rendelt szerepköröket.
-   Szükség esetén engedélyezheti valakinek az alkalmazás-hozzáférési kérelmek jóváhagyását, hogy az informatikai csoportnak ne kelljen.
-   Opcionálisan akár 10 személyt is beállíthat, akik jóváhagyják az alkalmazáshoz való hozzáférést.
-   Opcionálisan engedélyezheti a felhasználók számára az alkalmazásba való bejelentkezéshez használható jelszavak megadását.
-   Opcionálisan automatikusan hozzárendelheti az önkiszolgáló hozzárendelt felhasználókat egy alkalmazás-szerepkörhöz.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Az önkiszolgáló alkalmazás-hozzáférés engedélyezése lehetővé teszi, hogy a felhasználók megtalálják a saját alkalmazásaikat

Az önkiszolgáló alkalmazás-hozzáférés lehetővé teszi a felhasználók számára az alkalmazások önfelderítését, opcionálisan lehetővé teszi, hogy az üzleti csoport jóváhagyja az alkalmazásokhoz való hozzáférést. Lehetővé teheti az üzleti csoport számára, hogy a saját alkalmazások oldalról közvetlenül a saját alkalmazások jelszavas egyszeri bejelentkezés alkalmazásokhoz rendelt hitelesítő adatokat kezelhesse a felhasználók számára.

Az önkiszolgáló alkalmazások alkalmazáshoz való hozzáférésének engedélyezéséhez kövesse az alábbi lépéseket:
1. Nyissa meg a [**Azure Portalt**](https://portal.azure.com/) , és jelentkezzen be **globális rendszergazdaként.**
2. Nyissa meg a **Azure Active Directory bővítményt** a bal oldali navigációs menü tetején található **összes szolgáltatás** lehetőség kiválasztásával.
3. Írja be a **"Azure Active Directory**" kifejezést a szűrő keresőmezőbe, és válassza ki a **Azure Active Directory** elemet.
4. Válassza ki a **vállalati alkalmazásokat** a Azure Active Directory bal oldali navigációs menüjéből.
5. A **minden alkalmazás** elemre kattintva megtekintheti az összes alkalmazás listáját.
   * Ha nem látja a használni kívánt alkalmazást, használja a **minden alkalmazás lista** tetején található **szűrő** vezérlőelemet, és állítsa a **show (megjelenítés** ) lehetőséget az **összes alkalmazásra.**
6. Válassza ki azt az alkalmazást, amely számára engedélyezni szeretné az önkiszolgáló hozzáférést a listáról.
7. Az alkalmazás betöltése után válassza az **önkiszolgáló** lehetőséget az alkalmazás bal oldali navigációs menüjében.
8. Az alkalmazás önkiszolgáló alkalmazásokhoz való hozzáférésének engedélyezéséhez kapcsolja be a **felhasználók számára az alkalmazáshoz való hozzáférés kérését?** váltson át az Igen értékre **.**
9. Ezután válassza ki azt a csoportot, amelyhez hozzá szeretné adni az alkalmazáshoz hozzáférést kérő felhasználókat, jelölje ki a címke melletti választót, **amelyhez hozzá kívánja adni a felhasználókat?** elemet, és válasszon ki egy csoportot.
10. Nem **kötelező:** Ha szeretné megkövetelni, hogy a felhasználók hozzáférhessenek-e az üzleti jóváhagyáshoz, akkor az **alkalmazáshoz való hozzáférés engedélyezése előtt állítsa be a jóváhagyás megkövetelése** beállítást. az **Igen**értékre vált.
11. **Opcionális: csak jelszó egyszeri bejelentkezést használó alkalmazásokhoz,** ha engedélyezni szeretné, hogy ezek az üzleti jóváhagyók megadják az alkalmazásnak a jóváhagyott felhasználók számára eljuttatott jelszavakat, állítsa be az **alkalmazáshoz tartozó felhasználók jelszavának engedélyezése a jóváhagyóknak beállítást?** váltás az **Igen**értékre.
12. Nem **kötelező:** Itt adhatja meg azokat az üzleti jóváhagyókat, akik számára engedélyezett az alkalmazáshoz való hozzáférés jóváhagyása. Válassza ki, **hogy ki jogosult az alkalmazáshoz való hozzáférés jóváhagyására?** Ezután válassza ki a legfeljebb 10 egyéni üzleti jóváhagyót.
    * A csoportok nem támogatottak.
13. Nem **kötelező:** **a szerepköröket közzétevő alkalmazások esetében**, ha az önkiszolgáló jóváhagyott felhasználókat szeretné hozzárendelni egy szerepkörhöz, jelölje ki a (z) elem melletti választót, amelyhez hozzá kívánja rendelni a **felhasználókat az alkalmazásban?** annak a szerepkörnek a kiválasztásához, amelyhez ezeket a felhasználókat hozzá kell rendelni.
14. Kattintson a felső **Mentés** gombra a befejezéshez.

Az önkiszolgáló alkalmazás-konfiguráció befejezése után a felhasználók megkereshetik az [alkalmazás-hozzáférési panelt](https://myapps.microsoft.com/) , és a **+ Hozzáadás** gombra kattintva megtalálhatják azokat az alkalmazásokat, amelyekhez engedélyezte az önkiszolgáló hozzáférést. Az üzleti jóváhagyók az [alkalmazás hozzáférési paneljén](https://myapps.microsoft.com/)is megtekinthetik az értesítéseket. Engedélyezheti az e-mailek értesítését, ha egy felhasználó hozzáférést kért egy olyan alkalmazáshoz, amelyhez jóváhagyás szükséges. 

Ezek a jóváhagyások csak az egyszeri jóváhagyási munkafolyamatokat támogatják, ami azt jelenti, hogy ha több jóváhagyót ad meg, akkor egyetlen jóváhagyó jóváhagyhatja az alkalmazáshoz való hozzáférést.

## <a name="things-to-check-if-self-service-isnt-working"></a>Az önkiszolgáló működésének ellenőrzéséhez szükséges dolgok
-   Győződjön meg arról, hogy a felhasználó vagy csoport engedélyezte az önkiszolgáló alkalmazás-hozzáférés kérését.
-   Győződjön meg arról, hogy a felhasználó a megfelelő helyre látogat az önkiszolgáló alkalmazás-hozzáféréshez. a felhasználók az alkalmazás- [hozzáférési panelre](https://myapps.microsoft.com/) léphetnek, és a **+ Hozzáadás** gombra kattintva megkereshetik azokat az alkalmazásokat, amelyekhez engedélyezte az önkiszolgáló hozzáférést.
-   Ha az önkiszolgáló alkalmazás-hozzáférés nemrég lett konfigurálva, próbálja meg újra bejelentkezni a felhasználó hozzáférési paneljére néhány perc múlva, hogy megtörtént-e az önkiszolgáló hozzáférésének módosítása.

## <a name="next-steps"></a>Következő lépések
[Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre](../users-groups-roles/groups-self-service-management.md)
