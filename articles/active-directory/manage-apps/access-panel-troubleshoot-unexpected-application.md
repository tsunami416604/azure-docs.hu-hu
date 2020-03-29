---
title: Az alkalmazások megjelenésének a hozzáférési panelen | Microsoft dokumentumok
description: Hibaelhárítás, hogy miért jelenik meg egy alkalmazás a Hozzáférési panelen
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
ms.reviewr: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa8ea75cc7fda05326c802c25a91d025b66b5ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784416"
---
# <a name="how-applications-appear-on-the-access-panel"></a>Az alkalmazások megjelenésének megtervezése a hozzáférési panelen

A Hozzáférési panel egy webalapú portál, amely lehetővé teszi, hogy az Azure Active Directoryban (Azure AD) munkahelyi vagy iskolai fiókkal rendelkező felhasználó megtekintse és elindítsa azokat a felhőalapú alkalmazásokat, amelyekhez az Azure AD-rendszergazda hozzáférést biztosított számukra. Ezek az alkalmazások a felhasználó nevében vannak konfigurálva az Azure AD portálon. A rendszergazda kiépítheti az alkalmazást közvetlenül a felhasználónak vagy egy olyan csoportnak, amelynek része a felhasználó, ami azt eredményezi, hogy az alkalmazás megjelenik a felhasználó hozzáférési paneljén.

## <a name="general-issues-to-check-first"></a>Általános kérdések, amelyeket először ellenőrizni kell

-   Ha egy alkalmazást eltávolítottak egy olyan felhasználóból vagy csoportból, amelynek a felhasználó tagja, próbáljon meg néhány perc múlva újra bejelentkezni és kijelentkezni a felhasználó hozzáférési paneljén, hogy lássa, az alkalmazás eltávolításra kerül-e.

-   Ha egy licencet eltávolítottak egy felhasználótól vagy csoportból, a felhasználó ennek a tagja, a csoport méretétől és összetettségétől függően a módosítások végrehajtása hosszú időt vehet igénybe. Hagyjon több időt a hozzáférési panelre való bejelentkezés előtt.

## <a name="problems-related-to-assigning-applications-to-users"></a>Az alkalmazások felhasználókhoz való hozzárendelésével kapcsolatos problémák

Előfordulhat, hogy egy felhasználó azért lát egy alkalmazást a hozzáférési panelen, mert korábban hozzá volt rendelve. Az alábbiakban néhány módja annak ellenőrzésére:

-   [Annak ellenőrzése, hogy egy felhasználó hozzá van-e rendelve az alkalmazáshoz](#check-if-a-user-is-assigned-to-the-application)

-   [Annak ellenőrzése, hogy a felhasználó az alkalmazáshoz kapcsolódó licenc alatt áll-e](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Annak ellenőrzése, hogy egy felhasználó hozzá van-e rendelve az alkalmazáshoz

Annak ellenőrzéséhez, hogy egy felhasználó hozzá van-e rendelve az alkalmazáshoz, kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson az Azure Active Directory bal oldali navigációs **menüjében** az Enterprise Applications parancsra.

5. Kattintson **a Minden alkalmazás** elemre az összes alkalmazás listájának megtekintéséhez.

6. **Keresse meg** a kérdéses alkalmazás nevét.

7. kattintson a **Felhasználók és csoportok**elemre.

8. Ellenőrizze, hogy a felhasználó hozzá van-e rendelve az alkalmazáshoz.

   * Ha el szeretné távolítani a felhasználót az alkalmazásból, **kattintson a** felhasználó sorára, és válassza a **törlés**lehetőséget.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Annak ellenőrzése, hogy a felhasználó az alkalmazáshoz kapcsolódó licenc alatt áll-e

A felhasználó hozzárendelt licenceinek ellenőrzéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5. kattintson **a Minden felhasználó gombra.**

6. **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. Kattintson **a Licencek** elemre, ha meg szeretné tekinteni, hogy a felhasználó jelenleg mely licenceket rendelte hozzá.

   * Ha a felhasználó Office-licenchez van rendelve, ez lehetővé teszi, hogy a First Party Office alkalmazások megjelenjenek a felhasználó hozzáférési paneljén.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Az alkalmazások csoportokhoz való hozzárendelésével kapcsolatos problémák

Előfordulhat, hogy egy felhasználó egy alkalmazást lát a hozzáférési panelen, mert egy olyan csoport része, amelyhez az alkalmazás hozzá van rendelve. Az alábbiakban néhány módja annak ellenőrzésére:

-   [Felhasználó csoporttagságának ellenőrzése](#check-a-users-group-memberships)

-   [Annak ellenőrzése, hogy a felhasználó tagja-e egy licenchez rendelt csoportnak](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Felhasználó csoporttagságának ellenőrzése

A csoport tagságának ellenőrzéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5. kattintson **a Minden felhasználó gombra.**

6. **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. kattintson **a Csoportok gombra.**

8. Ellenőrizze, hogy a felhasználó tagja-e az alkalmazáshoz rendelt csoportnak.

   * Ha el szeretné távolítani a felhasználót a csoportból, **kattintson a** csoport sorára, és válassza a törlés lehetőséget.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Annak ellenőrzése, hogy a felhasználó tagja-e egy licenchez rendelt csoportnak

1. Nyissa meg az [**Azure Portalt,**](https://portal.azure.com/) és jelentkezzen be **globális rendszergazdaként.**

2. Nyissa meg az **Azure Active Directory-bővítményt** a bal oldali főnavigációs menü tetején található **Összes szolgáltatás** elemre kattintva.

3. Írja be az **"Azure Active Directory"** kifejezést a szűrő keresőmezőjébe, és válassza ki az **Azure Active Directory-elemet.**

4. kattintson a navigációs menü **Felhasználók és csoportok** parancsára.

5. kattintson **a Minden felhasználó gombra.**

6. **Keresse meg** az önt érdeklő felhasználót, és **kattintson a** kijelölni kívánt sorra.

7. kattintson **a Csoportok gombra.**

8. kattintson egy adott csoport sorára.

9. **Kattintson a Licencek** elemre, ha meg szeretné tekinteni, hogy a csoport mely licenceket rendelte hozzá.

   * Ha a csoport Office-licenchez van rendelve, ez lehetővé teheti, hogy bizonyos First Party Office alkalmazások megjelenjenek a felhasználó hozzáférési paneljén.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Ha ezek a hibaelhárítási lépések nem oldják meg a problémát

nyisson meg egy támogatási jegyet a következő információkkal, ha azok rendelkezésre állnak:

-   Korrelációs hiba azonosítója

-   UPN (felhasználói e-mail cím)

-   Bérlőazonosító

-   Böngésző típusa

-   Időzóna és idő/időkeret hiba esetén

-   Hegedűs nyomok

## <a name="next-steps"></a>További lépések
[Alkalmazások kezelése az Azure Active Directoryval](what-is-application-management.md)
