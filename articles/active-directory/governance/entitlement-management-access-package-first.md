---
title: Oktatóanyag – az első hozzáférési csomag létrehozása az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Lépésenkénti útmutató az első hozzáférési csomag létrehozásához a Azure Active Directory jogosultságok kezelése (előzetes verzió) szolgáltatásban.
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1688651466ba6748e1254c9d33bb24435602868b
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489169"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Oktatóanyag: Az első hozzáférési csomag létrehozása az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A szervezetek számára fontos funkció az összes erőforráshoz, például a csoportokhoz, alkalmazásokhoz és webhelyekhez való hozzáférés kezelése. Azt szeretné, hogy az alkalmazottak a megfelelő szintű hozzáférést biztosítsanak a hatékonysághoz, és el kell távolítaniuk a hozzáférést, ha már nincs rá szükség.

Ebben az oktatóanyagban a Woodgrove Bank rendszergazdaként működik. A rendszer arra kérte, hogy hozzon létre egy erőforrás-csomagot egy olyan webes projekthez, amelyet a belső felhasználók önkiszolgáló kérelemre használhatnak. A kérések jóváhagyást igényelnek, és a felhasználó hozzáférése 30 nap után lejár. Ebben az oktatóanyagban a webes projekt erőforrásai csak egyetlen csoport tagjai, de lehetnek csoportok, alkalmazások vagy SharePoint Online-webhelyek gyűjteményei.

![Forgatókönyv áttekintése](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférési csomag létrehozása erőforrásként egy csoporttal
> * Jóváhagyó kijelölése
> * Bemutatjuk, hogyan kérheti a belső felhasználó a hozzáférési csomagot
> * Hozzáférési kérelem jóváhagyása

Ha nem rendelkezik prémium szintű Azure AD P2 vagy Enterprise Mobility + Security E5 licenccel, hozzon létre egy ingyenes [Enterprise Mobility + Security E5 próbaverziót](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-jogosultságok felügyeletének (előzetes verzió) használatához a következő licencek egyike szükséges:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-licenc

## <a name="step-1-set-up-users-and-group"></a>1\. lépés: Felhasználók és csoportok beállítása

Egy erőforrás-címtárhoz egy vagy több megosztani kívánt erőforrás tartozik. Ebben a lépésben létrehoz egy **Engineering Group** nevű csoportot a Woodgrove Bank címtárában, amely a jogosultságok kezelésének célként megadott erőforrása. A belső kérelmezőt is be kell állítania.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

![Felhasználók és csoportok létrehozása](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként vagy felhasználói rendszergazdaként.  

1. A bal oldali navigációs sávon kattintson a **Azure Active Directory**elemre.

1. Hozza létre vagy konfigurálja a következő két felhasználót. Ezeket a neveket vagy más neveket is használhatja. A **Rendszergazda1** lehet az a felhasználó, aki jelenleg be van jelentkezve.

    | Name (Név) | Címtárszerepkör | Leírás |
    | --- | --- | --- |
    | **Rendszergazda1** | Globális rendszergazda<br/>– vagy –<br/>Korlátozott rendszergazda (felhasználói rendszergazda) | Rendszergazda és jóváhagyó |
    | **Requestor1** | Felhasználó | Belső kérelmező |

    Ebben az oktatóanyagban a rendszergazda és a jóváhagyó ugyanaz a személy, de általában egy vagy több személyt jelöl ki jóváhagyóknak.

1. Hozzon létre egy **Engineering Group** nevű Azure ad biztonsági csoportot a **hozzárendelt**tagsági típussal.

    Ez a csoport lesz a jogosultságok kezelésének cél erőforrása. A csoportnak üresnek kell lennie a tagoktól a kezdéshez.

## <a name="step-2-create-an-access-package"></a>2\. lépés: Hozzáférési csomag létrehozása

A *hozzáférési csomag* az összes olyan erőforrás kötegét képezi, amelyet a felhasználónak egy projekten kell dolgoznia, vagy feladatait kell végrehajtania. A hozzáférési csomagok a katalógusok nevű tárolókban vannak meghatározva. Ebben a lépésben létrehoz egy **webes projekt-hozzáférési csomagot** az **általános** katalógusban.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

![Hozzáférési csomag létrehozása](./media/entitlement-management-access-package-first/elm-access-package.png)

1. A Azure Portal a bal oldali navigációs sávon kattintson a **Azure Active Directory**elemre.

1. A bal oldali menüben kattintson az **identitások szabályozása** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok**elemre.  Ha a **hozzáférés**megtagadva érték jelenik meg, győződjön meg arról, hogy a könyvtárban van egy prémium szintű Azure ad P2-licenc.

1. Kattintson az **új hozzáférési csomag**elemre.

    ![Jogosultságok kezelése a Azure Portal](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Az **alapvető beállítások** lapon írja be a **web Project Access Package** és a Description **Access Package nevet a mérnöki webes projekthez**.

1. Hagyja meg a **katalógus** legördülő lista **általános**értékre állítását.

    ![Új hozzáférési csomag – alapismeretek lap](./media/entitlement-management-access-package-first/basics.png)

1. Kattintson a **tovább** gombra az **erőforrás-szerepkörök** lap megnyitásához.

    Ezen a lapon kiválaszthatja a hozzáférési csomagban szerepeltetni kívánt engedélyeket.

1. Kattintson a **csoportok**elemre.

1. A csoportok kiválasztása panelen keresse meg és válassza ki a korábban létrehozott **mérnöki csoport** csoportot.

    Alapértelmezés szerint az **általános** katalóguson belüli és kívüli csoportok láthatók. Ha kijelöl egy csoportot az **általános** katalóguson kívül, a rendszer hozzáadja az **általános** katalógushoz.

    ![Új hozzáférési csomag – erőforrás-szerepkörök lap](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Kattintson a **kiválasztás** elemre a csoport listához való hozzáadásához.

1. A **szerepkör** legördülő listában válassza a **tag**elemet.

    ![Új hozzáférési csomag – erőforrás-szerepkörök lap](./media/entitlement-management-access-package-first/resource-roles.png)

1. Kattintson a **tovább** gombra a **házirend** lap megnyitásához.

1. Az **első szabályzat létrehozása** váltógomb beállítása **később**értékre.

    A szabályzatot a következő szakaszban fogja létrehozni.

    ![Új hozzáférési csomag – házirend lap](./media/entitlement-management-access-package-first/policy.png)

1. A **tovább** gombra kattintva nyissa meg a **felülvizsgálat + létrehozás** lapot.

    ![Új hozzáférési csomag – áttekintés + Létrehozás lap](./media/entitlement-management-access-package-first/review-create.png)

1. Tekintse át a hozzáférési csomag beállításait, majd kattintson a **Létrehozás**gombra.

    Előfordulhat, hogy megjelenik egy üzenet arról, hogy a hozzáférési csomag nem jelenik meg a felhasználók számára, mert a katalógus még nincs engedélyezve.

    ![Új hozzáférési csomag – nem látható üzenet](./media/entitlement-management-access-package-first/not-visible.png)

1. Kattintson az **OK** gombra.

    Néhány pillanat múlva megjelenik egy értesítés arról, hogy a hozzáférési csomag létrehozása sikeres volt.

## <a name="step-3-create-a-policy"></a>3\. lépés: Házirend létrehozása

A *szabályzatok határozzák* meg a hozzáférési csomag elérésére vonatkozó szabályokat vagy guardrails. Ebben a lépésben egy olyan házirendet hoz létre, amely lehetővé teszi egy adott felhasználó számára az erőforrás-címtárban a hozzáférési csomag kérését. Azt is megadhatja, hogy a kéréseket jóvá kell hagyni, és ki lesz a jóváhagyó.

![Hozzáférési csomag szabályzatának létrehozása](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. A **webes projekt-hozzáférési csomag**bal oldali menüjében kattintson a **házirendek**elemre.

    ![Hozzáférési csomag házirendjeinek listája](./media/entitlement-management-access-package-first/policies-list.png)

1. Kattintson a **házirend hozzáadása** elemre a szabályzat létrehozásához.

1. Írja be a **belső kérelmező házirend** nevét és leírását, **hogy a címtárban lévő felhasználók hozzáférést kérjenek a webes projekt erőforrásaihoz**.

1. A **hozzáférést kérő felhasználók** területen kattintson a **címtárban lévő felhasználók**elemre.

    ![Szabályzat létrehozása](./media/entitlement-management-access-package-first/policy-create.png)

1. Görgessen le a **felhasználók és csoportok kiválasztása** szakaszban, majd kattintson a **felhasználók és csoportok hozzáadása**lehetőségre.

1. A felhasználók és csoportok kiválasztása panelen válassza ki a korábban létrehozott **Requestor1** -felhasználót, majd kattintson a **kiválasztás**gombra.

1. A **kérelem** szakaszban állítsa az **Igen**értékre a **jóváhagyás** megkövetelése beállítást.

1. A **jóváhagyók kiválasztása** szakaszban kattintson a **Jóváhagyók hozzáadása**elemre.

1. A jóváhagyók kiválasztása panelen válassza ki a korábban létrehozott **Rendszergazda1** , majd kattintson a **kiválasztás**gombra.

    Ebben az oktatóanyagban a rendszergazda és a jóváhagyó ugyanaz a személy, de egy másik személyt is kijelölhet jóváhagyóként.

1. A **lejárat** szakaszban állítsa be a **hozzáférési csomag érvényességét** **napok száma**szerint.

1. A hozzáférés beállítása **30** nap **után lejár** .

1. A **házirend engedélyezése**beállításnál kattintson az **Igen**gombra.

    ![Házirend-beállítások létrehozása](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. A **belső kérelmező házirendjének**létrehozásához kattintson a **Létrehozás** gombra.

1. A webes projekt-hozzáférési csomag bal oldali menüjében kattintson az **Áttekintés**elemre.

1. Másolja a **saját hozzáférési portál hivatkozást**.

    Ezt a hivatkozást fogja használni a következő lépéshez.

    ![Hozzáférési csomag áttekintése – saját hozzáférési portál hivatkozása](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>4\. lépés: Hozzáférés kérése

Ebben a lépésben a lépéseket a **belső kérelmezőnek** kell elvégeznie, és hozzáférést kell kérnie a hozzáférési csomaghoz. A kérelmező a saját hozzáférési portál nevű hely használatával küldi el a kérelmeit. A saját hozzáférési portál lehetővé teszi, hogy a kérők beküldjék a hozzáférési csomagokat, és megtekintsék a hozzáférési csomagokat, amelyekre már hozzáférhetnek, és megtekinthetik a kérelmek előzményeit.

**Előfeltételként szükséges szerepkör:** Belső kérelmező

1. Jelentkezzen ki a Azure Portalból.

1. Egy új böngészőablakban navigáljon az előző lépésben másolt saját hozzáférési portál hivatkozásra.

1. Jelentkezzen be a saját hozzáférési portálra **Requestor1**néven.

    Ekkor meg kell jelennie a **webes projekt-hozzáférési csomagnak**.

1. Szükség esetén a **Leírás** oszlopban kattintson a nyílra a hozzáférési csomag részleteinek megtekintéséhez.

    ![Hozzáférési portál – hozzáférési csomagok](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kattintson a pipa jelre a csomag kiválasztásához.

1. Kattintson a **hozzáférés kérése** elemre a hozzáférés kérése panel megnyitásához.

1. Az **üzleti indoklás** mezőbe írja be a **webes projekttel kapcsolatos**indoklást.

1. Az **adott időszakra vonatkozó kérést** állítsa **Igen**értékre.

1. Állítsa a **kezdő dátumot** a mai napra  és a befejezési dátumra a holnap értékre.

    ![Hozzáférési portál – hozzáférés kérése](./media/entitlement-management-shared/my-access-request-access.png)

1. Kattintson a **Submit** (Küldés) gombra.

1. A bal oldali menüben kattintson a **kérelmek előzményei** elemre, és ellenőrizze, hogy elküldte-e a kérést.

## <a name="step-5-approve-access-request"></a>5\. lépés: Hozzáférési kérelem jóváhagyása

Ebben a lépésben a **jóváhagyó** felhasználóként jelentkezik be, és jóváhagyja a belső kérelmező hozzáférési kérelmét. A jóváhagyók ugyanazt a saját hozzáférési portált használják, mint a kérelmek küldésére kérők. A saját hozzáférési portál használatával a jóváhagyók megtekinthetik a függőben lévő jóváhagyásokat, és jóváhagyják vagy megtagadják a kérelmeket.

**Előfeltételként szükséges szerepkör:** Jóváhagyó

1. Jelentkezzen ki a saját hozzáférési portálról.

1. Jelentkezzen be a [saját hozzáférési](https://myaccess.microsoft.com) portálra **Rendszergazda1**néven.

1. A bal oldali menüben kattintson a **jóváhagyások**elemre.

1. A **függőben lévő** lapon keresse meg a **Requestor1**.

    Ha nem látja a Requestor1 érkező kérést, várjon néhány percet, és próbálkozzon újra.

1. Kattintson a **nézet** hivatkozásra a hozzáférési kérés panel megnyitásához.

1. Kattintson a **jóváhagyás**gombra.

1. Az **OK** mezőbe írja be a **webes projekthez jóváhagyott elérési**okot.

    ![Hozzáférési portál – hozzáférési kérelem](./media/entitlement-management-shared/my-access-approve-request.png)

1. A döntés elküldéséhez kattintson a **Submit (Küldés** ) gombra.

    Ekkor egy üzenetnek kell megjelennie, hogy sikeresen jóváhagyták.

## <a name="step-6-validate-that-access-has-been-assigned"></a>6\. lépés: Annak ellenőrzése, hogy a hozzáférés hozzá van-e rendelve

Most, hogy jóváhagyta a hozzáférési kérést, ebben a lépésben megerősíti, hogy a **belső kérelmező** hozzá lett rendelve a hozzáférési csomaghoz, és hogy most már a **mérnöki csoport** csoport tagja.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. Jelentkezzen ki a saját hozzáférési portálról.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) **Rendszergazda1**.

1. Kattintson a **Azure Active Directory** , majd az **identitás-szabályozás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok**elemre.

1. Keresse meg és kattintson a **webes projekt-hozzáférési csomag**elemre.

1. A bal oldali menüben kattintson a **kérelmek**elemre.

    Ekkor meg kell jelennie a Requestor1 és a belső kérelmező házirendnek, amelynek állapota **kézbesítés**.

1. A kérelem részleteinek megtekintéséhez kattintson a kérelemre.

    ![Hozzáférési csomag – kérelem részletei](./media/entitlement-management-access-package-first/request-details.png)

1. A bal oldali navigációs sávon kattintson a **Azure Active Directory**elemre.

1. Kattintson a **csoportok** elemre, és nyissa meg a **mérnöki csoport** csoportot.

1. Kattintson a **tagok**elemre.

    Ekkor meg kell jelennie a tag **Requestor1** .

    ![Mérnöki csoport tagjai](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>7\. lépés: Az erőforrások eltávolítása

Ebben a lépésben eltávolítja az elvégzett módosításokat, és törli a **webes projekt hozzáférési** csomagjának hozzáférési csomagját.

**Előfeltételként szükséges szerepkör:**  Globális rendszergazda vagy felhasználói rendszergazda

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. Nyissa meg a **webes projekt-hozzáférési csomagot**.

1. Kattintson a hozzárendelések elemre.

1. A **Requestor1**kattintson a három pontra ( **...** ), majd a **hozzáférés eltávolítása**elemre.

    Az állapot a kézbesítéstől a Lejártig lesz módosítva.

1. Kattintson a **házirendek**elemre.

1. A **belső kérelmező házirendjénél**kattintson a három pontra ( **..** .), majd a **Törlés**gombra.

1. Kattintson az **erőforrás-szerepkörök**elemre.

1. A **mérnöki csoportok**esetében kattintson a három pontra ( **...** ), majd az **erőforrás-szerepkör eltávolítása**elemre.

1. Nyissa meg a hozzáférési csomagok listáját.

1. **Webes projekt-hozzáférési projekt**esetén kattintson a három pontra ( **..** .), majd a **Törlés**elemre.

1. A Azure Active Directory törölje a létrehozott felhasználókat, például a **Requestor1** és a **Rendszergazda1**.

1. A **mérnöki csoport** csoportjának törlése.

## <a name="next-steps"></a>További lépések

A következő cikkből megismerheti a jogosultságok kezelésének gyakori forgatókönyveit.
> [!div class="nextstepaction"]
> [Gyakori forgatókönyvek](entitlement-management-scenarios.md)
