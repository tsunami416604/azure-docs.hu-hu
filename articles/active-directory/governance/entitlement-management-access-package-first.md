---
title: Oktatóanyag – az első hozzáférési csomag létrehozása az Azure ad-ben tagjogosultság-kezelés (előzetes verzió) – az Azure Active Directory
description: Lépésenkénti útmutató az első hozzáférési csomag létrehozása az Azure Active Directory tagjogosultság-kezelés (előzetes verzió).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 04/27/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 354af736d5896214848205f41e429d9bf2c49863
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873500"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Oktatóanyag: Az első hozzáférési csomag létrehozása az Azure ad-ben tagjogosultság-kezelés (előzetes verzió)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Kezelik az összes erőforrások az alkalmazottak szükség van, például a csoportokat, alkalmazásokat és webhelyeket, a szervezetek számára olyan fontos funkciókat. Szeretné biztosítása az alkalmazottak a megfelelő hozzáférési szintet, munkavégzést, és távolítsa el a hozzáférésüket, ha már nincs szükség van szükségük.

Ebben az oktatóanyagban munkához a Woodgrove Bank informatikai rendszergazdaként. Azt kérte, hogy a belső felhasználók is önkiszolgáló kérelem webes projektet az erőforrások csomagot hozhat létre. Kérelmek jóváhagyása szükséges, és a felhasználó hozzáférése 30 nap után lejár. Ebben az oktatóanyagban a web projektet erőforrások csak egyetlen csoportbeli tagság, de lehet, hogy csoportok, alkalmazások vagy SharePoint Online-webhelyekkel gyűjteménye.

![Forgatókönyv áttekintése](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférés csomag létrehozása egy csoportot erőforrásai
> * Egy jóváhagyó kijelölése
> * Bemutatják, hogyan egy belső felhasználót kérhetnek-e a hozzáférés-csomag
> * A hozzáférési kérelem jóváhagyása

Ha nem rendelkezik egy Azure AD Premium P2 vagy Enterprise Mobility + Security E5 licenc, hozzon létre egy ingyenes [Enterprise Mobility + Security E5 próbaverziójának](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD tagjogosultság-kezelés (előzetes verzió) használatához kell rendelkeznie a következő licencek egyikét:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5 licenc

## <a name="step-1-set-up-users-and-group"></a>1. lépés: Felhasználók és csoportok beállítása

Egy erőforrás-könyvtár rendelkezik egy vagy több erőforrás megosztására. Ebben a lépésben létrehoz egy csoportot nevű **műszaki osztály csoport** , amely a célként megadott erőforrás tagjogosultság-kezelés a Woodgrove Bank a címtárban. Egy belső kérelmező is beállította.

**Előfeltétel szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

![Felhasználók és csoportok létrehozása](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy globális rendszergazdai vagy a felhasználó rendszergazdaként.  

1. A bal oldali navigációs sávján kattintson **Azure Active Directory**.

1. Hozzon létre, vagy konfigurálja a következő két felhasználót. Ezeket a neveket vagy különböző neveket is használhatja. **Rendszergazda1** , jelenleg be van jelentkezve, a felhasználó lehet.

    | Name (Név) | Címtárszerepkör | Leírás |
    | --- | --- | --- |
    | **Admin1** | Globális rendszergazda<br/>– vagy –<br/>Korlátozott rendszergazda (felhasználói rendszergazda) | Rendszergazda és a jóváhagyó |
    | **Requestor1** | Felhasználó | Belső kérelmező |

    A jelen oktatóanyag esetében a rendszergazda és a jóváhagyó ugyanaz a személy, de általában által kijelölt jóváhagyónak kell egy vagy több személynek.

1. Hozzon létre egy Azure AD biztonsági csoport nevű **műszaki osztály csoport** tagsági típussal rendelkező **hozzárendelt**.

    Ez a csoport lesz a cél erőforrás tagjogosultság-kezelés. A csoport tagjainak elindításához üresnek kell lennie.

## <a name="step-2-create-an-access-package"></a>2. lépés: Hozzáférés csomag létrehozása

Egy *hozzáférés csomag* van egy kötegelt felhasználó számára a projekthez, vagy a feladat végrehajtásához szükséges összes erőforrást. Hozzáférési csomagok meghatározott nevű tárolók *katalógusok*. Ebben a lépésben hozzon létre egy **webes projekt hozzáférés csomag** a a **általános** katalógus.

**Előfeltétel szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

![Hozzáférés csomag létrehozása](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Az Azure Portalon, a bal oldali navigációs sávján kattintson **Azure Active Directory**.

1. A bal oldali menüben kattintson a **Identitáskezelést**

1. A bal oldali menüben kattintson a **csomagok eléréséhez**.  Ha látja **hozzáférés megtagadva**, győződjön meg arról, hogy megtalálható-e az Azure AD Premium P2-licenc ebben a könyvtárban.

1. Kattintson a **új hozzáférési csomag**.

    ![Tagjogosultság-kezelés az Azure Portalon](./media/entitlement-management-access-package-first/access-packages-list.png)

1. Az a **alapjai** fülre, írja be a nevet **webes projekt hozzáférés csomag** és egy leírást **hozzáférés csomag, a mérnöki csapathoz webes projekt**.

1. Hagyja a **katalógus** legördülő listából válassza ki állítsa be **általános**.

    ![Új hozzáférési package - alapismeretek lap](./media/entitlement-management-access-package-first/basics.png)

1. Kattintson a **tovább** megnyitásához a **erőforrás-szerepkörökkel** fülre.

    Ezen a lapon jelölje be az engedélyeket a hozzáférés csomaghoz.

1. Kattintson a **csoportok**.

1. Válassza ki a csoportokat ablaktábláján keresse meg és válassza a **műszaki osztály csoport** korábban létrehozott csoportot.

    Alapértelmezés szerint belüli és kívüli csoportokat láthatja a **általános** katalógus. Amikor kiválaszt egy csoportot az alkalmazáson kívül a **általános** katalógus, azt fogja hozzáadni a **általános** katalógus.

    ![Új hozzáférési package - erőforrás szerepkörök lap](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Kattintson a **kiválasztása** a csoport hozzáadása a listához.

1. Az a **szerepkör** legördülő listában válassza **tag**.

    ![Új hozzáférési package - erőforrás szerepkörök lap](./media/entitlement-management-access-package-first/resource-roles.png)

1. Kattintson a **tovább** megnyitásához a **házirend** fülre.

1. Állítsa be a **első szabályzat létrehozása** kapcsolót **később**.

    A következő szakaszban fog létrehozni a szabályzatot.

    ![Új hozzáférési package - házirend lap](./media/entitlement-management-access-package-first/policy.png)

1. Kattintson a **tovább** megnyitásához a **felülvizsgálat + létrehozás** fülre.

    ![Új hozzáférési csomag – Áttekintés + lap létrehozása](./media/entitlement-management-access-package-first/review-create.png)

1. Tekintse át a hozzáférés csomag beállításait, és kattintson a **létrehozás**.

    Előfordulhat, hogy a hozzáférés-csomag nem lesz látható a felhasználók számára, mert a katalógusban még nem érhető üzenet jelenik meg.

    ![Új hozzáférési package - üzenet nem látható](./media/entitlement-management-access-package-first/not-visible.png)

1. Kattintson az **OK** gombra.

    Néhány pillanat múlva megjelenik egy értesítés, hogy a hozzáférés-csomag létrehozása sikerült.

## <a name="step-3-create-a-policy"></a>3. lépés: Szabályzat létrehozása

A *házirend* a szabályok vagy guardrails eléréséhez egy hozzáférés-csomagot határoz meg. Ebben a lépésben egy szabályzatot, amely lehetővé teszi, hogy egy adott felhasználó az erőforrás-könyvtár kérhet a hozzáférés-csomag létrehozása. Emellett megadhatja, hogy a kérelem jóvá kell hagyni, és kinek lesz a jóváhagyó.

![Csomag hozzáférési szabályzat létrehozása](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Előfeltétel szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. Az a **webes projekt hozzáférés csomag**, a bal oldali menüben kattintson a **házirendek**.

    ![Hozzáférési házirendek csomaglista](./media/entitlement-management-access-package-first/policies-list.png)

1. Kattintson a **szabályzat hozzáadása** megnyitásához a szabályzat létrehozása.

1. Írja be a nevét **belső kérelmező házirend** és a leírás **lehetővé teszi a felhasználóknak ebben a könyvtárban való hozzáférését a webes projekt erőforrások**.

1. Az a **felhasználók, akik hozzáférést** területén kattintson **a felhasználók számára a címtár**.

    ![Szabályzat létrehozása](./media/entitlement-management-access-package-first/policy-create.png)

1. Görgessen le a **válassza ki a felhasználók és csoportok** szakaszt, és kattintson a **felhasználók és csoportok hozzáadása**.

1. A kiválasztott felhasználók és csoportok panelen, jelölje be a **Requestor1** , és kattintson a korábban létrehozott felhasználói **válassza**.

1. Az a **kérelem** szakaszában **jóváhagyás megkövetelése,** való **Igen**.

1. Az a **jóváhagyók kiválasztása** területén kattintson **jóváhagyók hozzáadása**.

1. Válassza ki a jóváhagyók panelén válassza a **rendszergazda1** korábban létrehozott, és kattintson a **kiválasztása**.

    A jelen oktatóanyag esetében a rendszergazda és a jóváhagyó ugyanazt az embert, de megadhat más személy jóváhagyóként.

1. Az a **lejárati** szakaszában **hozzáférés csomag lejár** való **napok száma**.

1. Állítsa be **hozzáférés lejárata után** való **30** nap.

1. A **házirend engedélyezése**, kattintson a **Igen**.

    ![A házirend-beállítások létrehozása](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Kattintson a **létrehozás** hozhat létre a **belső kérelmező házirend**.

1. A webes projekt hozzáférés csomag bal oldali menüben kattintson a **áttekintése**.

1. Másolás a **saját hozzáférési portál hivatkozása**.

    Ez a hivatkozás a következő lépésben fogja használni.

    ![Hozzáférés-csomag – áttekintés – saját hozzáférési portál hivatkozása](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>4. lépés: Hozzáférés kérése

Ebben a lépésben, hajtsa végre a lépéseket, a **belső kérelmező** , és kérjen hozzáférést a hozzáférés csomaghoz. Kérelmezőktől a hozzáférést nyújt a kérelmek egy helyet, a saját hozzáférési portál nevű. A saját hozzáférési portál lehetővé teszi, hogy a kérelmezőktől a hozzáférést a hozzáférési csomagok kérelmek elküldése, tekintse meg a hozzáférési csomagok, már rendelkezik hozzáféréssel, és a kérelmek előzményeinek megtekintése.

**Előfeltétel szerepkör:** Belső kérelmező

1. Jelentkezzen ki az Azure Portalon.

1. Egy új böngészőablakban keresse meg a saját hozzáférési portál az előző lépésben kimásolt hivatkozást.

1. Jelentkezzen be a saját hozzáférés portált **Requestor1**.

    Megtekintheti a **webes projekt hozzáférés csomag**.

1. Ha szükséges, az a **leírás** oszlopot, kattintson a nyílra kattintva megtekintheti a hozzáférési csomag részleteit.

    ![A hozzáférési portál – a hozzáférési csomagok](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kattintson a pipa jelre a csomagot.

1. Kattintson a **hozzáférés kérése** a kérelem hozzáférési panel megnyitásához.

1. Az a **üzleti indoklás** mezőbe írja be az indoklást **dolgozik a webes projekt**.

1. Állítsa be a **adott időszakra vonatkozó kérelem** kapcsolót **Igen**.

1. Állítsa be a **kezdő dátum** a ma és **befejező dátum** a holnap innovációit.

    ![A hozzáférési portál – hozzáférés kéréséhez](./media/entitlement-management-shared/my-access-request-access.png)

1. Kattintson a **Submit** (Küldés) gombra.

1. A bal oldali menüben kattintson a **korábbi kérések** , győződjön meg arról, hogy a kérelme el lett küldve.

## <a name="step-5-approve-access-request"></a>5. lépés: Hozzáférési kérelem jóváhagyása

Ebben a lépésben jelentkezik be, a **jóváhagyó** felhasználói és a belső kérelmező a hozzáférési kérelem jóváhagyása. Jóváhagyók a portállal azonos saját hozzáférési kérelmek elküldéséhez használja a kérelmezőktől a hozzáférést. A saját hozzáférési portál használata esetén a jóváhagyók függőben lévő jóváhagyások megtekintése és jóváhagyhatják vagy megtagadhatják a kérelmeket.

**Előfeltétel szerepkör:** Jóváhagyó

1. Jelentkezzen ki a saját hozzáférés-portálról.

1. Jelentkezzen be a [saját hozzáférési portál](https://myaccess.microsoft.com) , **rendszergazda1**.

1. A bal oldali menüben kattintson a **jóváhagyások**.

1. Az a **függőben lévő** lapon, a Keresés **Requestor1**.

    Ha nem látja a kérés Requestor1, várjon néhány percet, és próbálkozzon újra.

1. Kattintson a **nézet** hivatkozásra a hozzáférési kérés panel megnyitásához.

1. Kattintson a **jóváhagyása**.

1. Az a **OK** mezőbe írja be a OK **hozzáférés a webes projekt jóváhagyott**.

    ![A hozzáférési portál – a hozzáférési kérelem](./media/entitlement-management-shared/my-access-approve-request.png)

1. Kattintson a **küldés** döntéseiben elküldéséhez.

    Megjelenik egy üzenet, hogy sikeresen jóvá van hagyva.

## <a name="step-6-validate-that-access-has-been-assigned"></a>6. lépés: Ellenőrizze, hogy hozzáférést kapott

Most, hogy a hozzáférési kérelem ebben a lépésben hagyott jóvá, ellenőrizze, hogy a **belső kérelmező** lett hozzárendelve hozzáférés csomagot, és a most már tagja a **műszaki osztály csoport** csoport.

**Előfeltétel szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. Jelentkezzen ki a saját hozzáférés-portálról.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , a **rendszergazda1**.

1. Kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez**.

1. Keresse meg és kattintson a **webes projekt hozzáférés csomag**.

1. A bal oldali menüben kattintson a **kérelmek**.

    Megtekintheti az Requestor1 és a belső kérelmező házirend állapottal **kézbesítések**.

1. Kattintson a kérelem a kérelem részletes adatainak megtekintéséhez.

    ![Access-package - kérelem részletei](./media/entitlement-management-access-package-first/request-details.png)

1. A bal oldali navigációs sávján kattintson **Azure Active Directory**.

1. Kattintson a **csoportok** , és nyissa meg a **műszaki osztály csoport** csoport.

1. Kattintson a **tagok**.

    Megtekintheti az **Requestor1** felsorolt tagként.

    ![Műszaki osztály csoport tagjai](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>7. lépés: Az erőforrások eltávolítása

Ebben a lépésben végrehajtott módosításokat távolítsa el, és törölje a **webes projekt hozzáférés csomag** hozzáférés csomagot.

**Előfeltétel szerepkör:**  Globális rendszergazda vagy felhasználói rendszergazda

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. Nyissa meg **webes projekt hozzáférés csomag**.

1. Kattintson a **hozzárendelések**.

1. A **Requestor1**, kattintson a három pont (**...** ) majd **hozzáférés eltávolítása**.

    Az állapot kézbesítések lejárt állapotúra változik.

1. Kattintson a **házirendek**.

1. A **belső kérelmező házirend**, kattintson a három pont (**...** ) majd **törlése**.

1. Kattintson a **erőforrás-szerepkörökkel**.

1. A **műszaki osztály csoport**, kattintson a három pont (**...** ) majd **Remove erőforrás szerepkör**.

1. Nyissa meg a hozzáférési csomagok listáját.

1. A **webes projekt hozzáférés projekt**, kattintson a három pont (**...** ) majd **törlése**.

1. Az Azure Active Directoryban, például a létrehozott felhasználók törlése **Requestor1** és **rendszergazda1**.

1. Törölje a **műszaki osztály csoport** csoport.

## <a name="next-steps"></a>További lépések

Folytassa a következő cikkel kapcsolatos tagjogosultság-kezelés gyakori forgatókönyv lépéseit.
> [!div class="nextstepaction"]
> [Gyakori forgatókönyvek](entitlement-management-scenarios.md)
