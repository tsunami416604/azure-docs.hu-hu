---
title: Oktatóanyag – Hozzáférési csomag létrehozása – Azure AD-jogosultságkezelés
description: Részletes oktatóanyag az első hozzáférési csomag létrehozásáról az Azure Active Directory jogosultságkezelésében.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 03/30/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2d31ef46dfba31a8f217f68e8d5f98b67d58da5
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410590"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Oktatóanyag: Hozza létre az első hozzáférési csomagot az Azure AD-jogosultságkezelésben

Az alkalmazottak számára szükséges erőforrásokhoz , például csoportokhoz, alkalmazásokhoz és webhelyekhez való hozzáférés kezelése fontos funkció a szervezetek számára. Azt szeretné, hogy az alkalmazottak a megfelelő szintű hozzáférést kell a produktív, és távolítsa el a hozzáférést, ha már nincs szükség.

Ebben az oktatóanyagban a Woodgrove Bank informatikai rendszergazdaként dolgozik. A rendszer arra kérte, hogy hozzon létre egy erőforráscsomagot egy olyan marketingkampányhoz, amelyet a belső felhasználók önkiszolgáló kérésre kérhetnek. A kérelmek nem igényelnek jóváhagyást, és a felhasználói hozzáférés 30 nap után lejár. Ebben az oktatóanyagban a marketingkampány erőforrásai csak egyetlen csoportban vannak, de csoportok, alkalmazások vagy SharePoint Online-webhelyek gyűjteménye lehet.

![Forgatókönyv áttekintése](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférési csomag létrehozása csoporttal erőforrásként
> * Hozzáférés kérése a címtárban lévő felhasználóknak
> * Annak bemutatása, hogy egy belső felhasználó hogyan kérheti a hozzáférési csomagot

Az Azure Active Directory jogosultságkezelésének üzembe helyezésének folyamatának részletes bemutatásához, beleértve az első hozzáférési csomag létrehozását, tekintse meg az alábbi videót:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD jogosultságkezelés használatához az alábbi licencek egyikével kell rendelkeznie:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5 licenc

További információt a [Licenckövetelmények című témakörben talál.](entitlement-management-overview.md#license-requirements)

## <a name="step-1-set-up-users-and-group"></a>1. lépés: Felhasználók és csoportok beállítása

Az erőforráskönyvtár nak egy vagy több megosztandó erőforrása van. Ebben a lépésben hozzon létre egy **marketing erőforrások** nevű csoportot a Woodgrove Bank könyvtárban, amely a jogosultságkezelés célerőforrása. Belső kérelmezőt is beállít.

**Előfeltételi szerepkör:** Globális rendszergazda vagy felhasználó

![Felhasználók és csoportok létrehozása](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) globális rendszergazdaként vagy felhasználóként.  

1. A bal oldali navigációs sávon kattintson az **Azure Active Directory**elemre.

1. A következő két felhasználó létrehozása vagy konfigurálása. Használhatja ezeket a neveket vagy különböző neveket. **Admin1** lehet az a felhasználó, akijelenleg be van jelentkezve.

    | Név | Címtárszerepkör |
    | --- | --- |
    | **Rendszergazda1** | Globális rendszergazda<br/>– vagy –<br/>Rendszergazda |
    | **Kérelmező1** | Felhasználó |

1. Hozzon létre egy Marketing típusú Azure AD biztonsági **csoportot,** amelynek tagsági típusa **Assigned.**

    Ez a csoport lesz a jogosultságkezelés célerőforrása. A csoport kezdési részének üresnek kell lennie.

## <a name="step-2-create-an-access-package"></a>2. lépés: Hozzáférési csomag létrehozása

A *hozzáférési csomag* olyan erőforrások egy kötege, amelyre egy csapatnak vagy projektnek szüksége van, és amelyet házirendek szabályoznak. Az access csomagok *katalógusok*nevű tárolókban vannak definiálva. Ebben a lépésben hozzon létre egy **marketingkampány-hozzáférési** csomagot az **Általános** katalógusban.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

![Hozzáférési csomag létrehozása](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Az Azure Portalon a bal oldali navigációs sávon kattintson az **Azure Active Directory**elemre.

2. A bal oldali menüben kattintson **az Identitás-irányítási parancsra.**

3. A bal oldali menüben kattintson az **Access-csomagok parancsra.**  Ha az **Access megtagadva**lehetőséget látja, győződjön meg arról, hogy egy Azure AD Premium P2 licenc található a címtárban.

4. Kattintson **az Új hozzáférési csomag gombra.**

    ![Jogosultságkezelés az Azure Portalon](./media/entitlement-management-shared/access-packages-list.png)

5. Az **Alapok** lapon írja be a **Marketingkampány-hozzáférési** csomag nevét és **leírását: Hozzáférés a kampány erőforrásaihoz.**

6. Hagyja a **Katalógus** legördülő listát **Általános**beállításra állítva.

    ![Új hozzáférési csomag – Alapok lap](./media/entitlement-management-access-package-first/basics.png)

7. Kattintson a **Tovább** gombra az **Erőforrás-szerepkörök** lap megnyitásához.

    Ezen a lapon kiválaszthatja a hozzáférési csomagba felvenni kívánt erőforrásokat és erőforrásszerepkört.

8. Kattintson **a Csoportok és csoportok gombra.**

9. A Csoportok kiválasztása ablaktáblában keresse meg és jelölje ki a korábban létrehozott **Marketing erőforrás** csoportot.

    Alapértelmezés szerint az **Általános** katalóguson belüli és kívüli csoportok jelennek meg. Ha az **Általános** katalóguson kívül kijelöl egy csoportot, az hozzáadódik az **Általános** katalógushoz.

    ![Új hozzáférési csomag – Erőforrás-szerepkörök lap](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

10. A **Kijelölés gombra** kattintva vegye fel a csoportot a listára.

11. A **Szerepkör** legördülő listában válassza a **Tag**lehetőséget.

    ![Új hozzáférési csomag – Erőforrás-szerepkörök lap](./media/entitlement-management-access-package-first/resource-roles.png)

    >[!NOTE]
    > Dinamikus [csoportok](../users-groups-roles/groups-create-rule.md) használatakor a tulajdonoson kívül más szerepkörök nem jelennek meg. Ez az elvárt működés.
    > ![Forgatókönyv áttekintése](./media/entitlement-management-access-package-first/dynamic-group-warning.png)

12. A **Tovább** gombra kattintva nyissa meg a **Kérések** lapot.

    Ezen a lapon létrehoz egy kérelemházirendet. A *házirend* határozza meg a hozzáférési csomagok eléréséhez vezető szabályokat vagy korlátokat. Hozzon létre egy házirendet, amely lehetővé teszi, hogy egy adott felhasználó az erőforráskönyvtárban kérje ezt a hozzáférési csomagot.

13. A **Felhasználók, akik hozzáférést kérhetnek,** kattintson **a Címa címtárban lévő felhasználók számára** elemre, majd a Konkrét felhasználók és **csoportok**elemre.

    ![Új hozzáférési csomag – Kérések lap](./media/entitlement-management-access-package-first/requests.png)

14. Kattintson **a Felhasználók és csoportok hozzáadása gombra.**

15. A Felhasználók és csoportok kiválasztása ablaktáblában jelölje ki a korábban létrehozott **Kérelmező1** felhasználót.

    ![Új hozzáférési csomag – Kérések lap – Felhasználók és csoportok kijelölése](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

16. Kattintson a **Kiválasztás** gombra.

17. Görgessen le a **Kérelmek jóváhagyása** és **engedélyezése** szakaszhoz.

18. Hagyja, **hogy**a **Jóváhagyás megkövetelése** nem lesz.

19. A **Kérelmek engedélyezése**területen kattintson az **Igen** gombra, ha engedélyezni szeretné, hogy a rendszer a létrehozást kérő hozzáférési csomagot igényeljen.

    ![Új hozzáférési csomag – Kérések lap – Jóváhagyás és kérelmek engedélyezése](./media/entitlement-management-access-package-first/requests-approval-enable.png)

20. Kattintson a **Tovább** **gombra** az Életciklus lap megnyitásához.

21. A **Lejárat** szakaszban állítsa be az **Access-csomag-hozzárendelések lejáratát** **A napok száma beállításra.**

22. A Hozzárendelések beállítása **30** nap **után lejár.**

    ![Új hozzáférési csomag – Életciklus lap](./media/entitlement-management-access-package-first/lifecycle.png)

23. Kattintson a **Tovább** gombra a **Véleményezés + Létrehozás** lap megnyitásához.

    ![Új hozzáférési csomag - Véleményezés + Létrehozás lap](./media/entitlement-management-access-package-first/review-create.png)

    Néhány pillanat múlva meg kell jelennie egy értesítést, hogy a hozzáférési csomag sikeresen létrejött.

24. A Marketingkampány hozzáférési csomag bal oldali menüjében kattintson **az Áttekintés gombra.**

25. Másolja a **Saját hozzáférés portál hivatkozását**.

    Ezt a hivatkozást fogja használni a következő lépéshez.

    ![Access-csomag áttekintése – A Saját Hozzáférés portál hivatkozása](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>3. lépés: Hozzáférés kérése

Ebben a lépésben hajtsa végre a lépéseket, mint a **belső kérelmező,** és hozzáférést kér a hozzáférési csomaghoz. A kérelmezők a Saját Hozzáférés portál on keresztül küldik el a kéréseket. A Saját hozzáférés portál lehetővé teszi a kérelmezők számára, hogy hozzáférési csomagokra vonatkozó kérelmeket küldjenek, megtekinthetik azokat a hozzáférési csomagokat, amelyekhez már rendelkeznek hozzáféréssel, és megtekinthetik a kérelemelőzményeiket.

**Előfeltételi szerepkör:** Belső kérelmező

1. Jelentkezzen ki az Azure Portalon.

1. Egy új böngészőablakban keresse meg az előző lépésben másolt Saját hozzáférés portál hivatkozást.

1. Jelentkezzen be a Saját hozzáférés portálra **Kereső1**néven.

    Meg kell jelennie a **marketingkampány** hozzáférési csomagjának.

1. Ha szükséges, a **Leírás** oszlopban kattintson a nyílra a hozzáférési csomag részleteinek megtekintéséhez.

    ![Saját Hozzáférési portál – Access-csomagok](./media/entitlement-management-shared/my-access-access-packages.png)

1. A csomag kijelöléséhez kattintson a pipára.

1. A **Hozzáférés kérése** ablaktábla megnyitásához kattintson a Hozzáférés kérése elemre.

    ![Saját hozzáférési portál – Hozzáférés kérése gomb](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. Az **Üzleti indoklás** mezőbe írja be az **új marketingkampányon dolgozó**indoklást.

    ![Saját hozzáférési portál – Hozzáférés kérése](./media/entitlement-management-shared/my-access-request-access.png)

1. Kattintson a **Küldés gombra.**

1. A bal oldali menüben kattintson az **Előzmények kérése** parancsra a kérelem elküldésének ellenőrzéséhez.

## <a name="step-4-validate-that-access-has-been-assigned"></a>4. lépés: Annak ellenőrzése, hogy a hozzáférés hozzá van-e rendelve

Ebben a lépésben megerősíti, hogy a **belső kérelmező** höz van rendelve a hozzáférési csomag, és hogy most már a **Marketing erőforráscsoport** tagja.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Jelentkezzen ki a Saját hozzáférés portálról.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) **rendszergazdaként1.**

1. Kattintson **az Azure Active Directory,** majd **az Identitáscégcégcég**elemre.

1. A bal oldali menüben kattintson az **Access-csomagok parancsra.**

1. Keresse meg és kattintsa **a Marketingkampány** hozzáférési csomagra.

1. A bal oldali menüben kattintson a **Kérések parancsra.**

    Meg kell jelennie a Requestor1 és a Kezdeti házirend nek **Kézbesítve**állapotúállapotban.

1. Kattintson a kérelemre a kérelem részleteinek megtekintéséhez.

    ![Access csomag - Részletek kérése](./media/entitlement-management-access-package-first/request-details.png)

1. A bal oldali navigációs sávon kattintson az **Azure Active Directory**elemre.

1. Kattintson **a Csoportok gombra,** és nyissa meg a **Marketing erőforrások** csoportot.

1. Kattintson **a Tagok gombra.**

    Meg kell jelennie **Requestor1** szerepel a tag.

    ![Marketingerőforrások tagjai](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>5. lépés: Erőforrások karbantartása

Ebben a lépésben eltávolítja a módosításokat, és törli a **Marketingkampány** hozzáférési csomagot.

**Előfeltételi szerepkör:**  Globális rendszergazda vagy felhasználó

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. Nyissa meg a **Marketingkampány** hozzáférési csomagot.

1. Kattintson **a Hozzárendelések gombra.**

1. A **Requestor1 esetében**kattintson a három pontra (**...**), majd a Hozzáférés **eltávolítása parancsra.** A megjelenő üzenetben kattintson az **Igen**gombra.

    Néhány pillanat múlva az állapot kézbesítve ről lejártra változik.

1. Kattintson **az Erőforrás-szerepkörök gombra.**

1. **Marketingerőforrások**esetén kattintson a három pontra (**...**), majd az **Erőforrás-szerepkör eltávolítása parancsra.** A megjelenő üzenetben kattintson az **Igen**gombra.

1. Nyissa meg a hozzáférési csomagok listáját.

1. **A marketingkampány ban**kattintson a három pontra (**...**), majd a **Törlés gombra.** A megjelenő üzenetben kattintson az **Igen**gombra.

1. Az Azure Active Directoryban törölje a létrehozott felhasználókat, például **a Requestor1** és **az Admin1**szolgáltatást.

1. Törölje a **Marketing erőforrások** csoportot.

## <a name="next-steps"></a>További lépések

Továbbadhat a következő cikkhez, és megismerhetjük a jogosultságkezelés gyakori forgatókönyvlépéseit.
> [!div class="nextstepaction"]
> [Gyakori forgatókönyvek](entitlement-management-scenarios.md)
