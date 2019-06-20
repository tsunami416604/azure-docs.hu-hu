---
title: Szerkesztése és kezelése az Azure ad-ben tagjogosultság-kezelés (előzetes verzió) – az Azure Active Directory meglévő hozzáférési csomag
description: Ismerje meg, hogyan szerkesztése és kezelése az Azure Active Directory tagjogosultság-kezelés (előzetes verzió) egy meglévő hozzáférési csomagot.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73c1717249abac30847f96073ee6daf7d98112d7
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190334"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Szerkesztése és kezelése az Azure ad-ben tagjogosultság-kezelés (előzetes verzió) egy meglévő hozzáférési csomagot

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Egy hozzáférési csomag lehetővé teszi, hogy az erőforrások és a szabályzatok egy egyszeri beállítás, amely automatikusan felügyeli a hozzáférést a hozzáférés csomag élettartama. Access-Csomagkezelőt, mint az erőforrások egy hozzáférési csomag módosíthatja bármikor nem kell bajlódnunk a felhasználói hozzáférés az új erőforrások kiépítése, vagy a korábbi erőforrások eltávolítása a hozzáférésüket. Házirendek is bármikor frissíthető, azonban a házirend módosításai csak hatással új hozzáfér.

Ez a cikk bemutatja, hogyan szerkesztheti, és a meglévő access-csomagok kezelése.

## <a name="add-resource-roles"></a>Erőforrás-szerepkörök hozzáadása

Egy erőforrás-szerepkör erőforrás tartozó engedélyeket gyűjteménye. Az erőforrások a felhasználók kérvényezhetik a kívánt elérhetővé tétele úgy, hogy erőforrás-szerepkörök hozzáadása a hozzáférés-csomag. Erőforrás-szerepkörökkel, a csoportokat, alkalmazásokat és SharePoint-webhelyek adhat hozzá.

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. A bal oldali menüben kattintson a **erőforrás-szerepkörökkel**.

1. Kattintson a **erőforrás-szerepkörök hozzáadása** erőforrás szerepkör hozzáadása csomag lap eléréséhez megnyitásához.

    ![Csomag eléréséhez – erőforrás-szerepkörök hozzáadása](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. Attól függően, hogy egy csoport, az alkalmazás vagy a SharePoint-webhely hozzáadása hajtsa végre a lépéseket erőforrás szerepkör az alábbi szakaszokban.

### <a name="add-a-group-resource-role"></a>Egy csoport erőforrás szerepkör hozzáadása

Tagjogosultság-kezelés felhasználóinak automatikus hozzáadása egy csoporthoz való hozzárendelése esetén egy hozzáférés-csomagot is. 

- Ha egy csoport része, egy hozzáférés-csomagot és a egy felhasználó hozzá van rendelve hozzáférés csomagot, a felhasználó adja hozzá a csoporthoz, ha még nem létezik.
- Amikor egy felhasználó hozzáférési csomagok hozzárendelésével lejár, eltávolítja a csoportból, kivéve, ha jelenleg rendelkezik egy másik hozzáférési csomag, amely tartalmazza a csoportot, a hozzárendelés.

Kiválaszthatja, hogy bármilyen Office 365-csoport vagy az Azure AD biztonsági csoportot.  A Rendszergazdák csoportnak sem a katalógus; adhat hozzá katalógus tulajdonosok adhat hozzá a katalógus minden olyan csoportot, ha azok a csoport tulajdonosa. Egy csoport kiválasztásakor vegye figyelembe a következő Azure AD-korlátozások:

- A felhasználó hozzáadásakor, többek között a Vendég tagként egy csoporthoz, megjelenik az adott csoport összes tagjával.
- Az Azure AD egy csoportot, amely a rendszer szinkronizálta tagsága nem módosítható a Windows Server Active Directory Azure AD Connect használatával.  
- Dinamikus csoportok tagsága nem lehet frissíteni a hozzáadásával vagy eltávolításával a tagja, így a dinamikus csoporttagságok nem alkalmasak a tagjogosultság-kezelés segítségével.

1. Az a **csomag el az erőforrás-szerepkörök hozzáadása** kattintson **csoportok** a válassza ki a csoportokat panel megnyitásához.

1. Válassza ki a csoportokat, a hozzáférés csomaghoz.

    ![Csomag eléréséhez – erőforrás-szerepkörök – válassza ki a csoportokat hozzáadása](./media/entitlement-management-access-package-edit/group-select.png)

1. Kattintson a **Kiválasztás** gombra.

1. Az a **szerepkör** listáról válassza ki **tulajdonosa** vagy **tag**.

    Általában válassza ki a szerepét. Ha a tulajdonos szerepkör, amely lehetővé teszi a felhasználók számára más tagok vagy a tulajdonosok hozzáadása vagy eltávolítása.

    ![Hozzáférés a csomagot,-erőforrás szerepkör, csoport hozzáadása](./media/entitlement-management-access-package-edit/group-role.png)

1. Kattintson a **Hozzáadás**lehetőségre.

    Azok a felhasználók a hozzáférés csomaghoz meglévő hozzárendelések automatikusan ennek a csoportnak a tagjai válnak, amikor felveszik.

### <a name="add-an-application-resource-role"></a>Egy alkalmazás erőforrás-szerepkör hozzáadása

Az Azure AD automatikusan felhasználók hozzáférés hozzárendelése egy Azure AD vállalati alkalmazást, beleértve a SaaS-alkalmazásokhoz és a szervezete azon alkalmazásairól az Azure AD, ha egy felhasználó hozzá van rendelve egy hozzáférési csomag összevont rendelkezhet. Amelyekbe beépül az Azure AD összevont egyszeri bejelentkezés révén az alkalmazások az Azure AD összevonási jogkivonatok az alkalmazáshoz hozzárendelt felhasználók állít ki.

Alkalmazások több szerepkört is rendelkezhetnek. Alkalmazás hozzáadásakor hozzáférés csomagjára, ha az alkalmazás több szerepköre van, szüksége lesz a adja meg a megfelelő szerepkört a felhasználókhoz.  Ha alkalmazásokat fejleszt, áttekintheti, több hogyan ezen szerepkörök biztosítják az alkalmazások a cikk útmutatást [a SAML-jogkivonatban kiadott szerepkörjogcím konfigurálása](../develop/active-directory-enterprise-app-role-management.md).

Ha egy alkalmazás-szerepkör része egy hozzáférés-csomagot:

- Amikor egy felhasználó hozzá van rendelve, hogy hozzáférési csomag, a felhasználó adnak hozzá, hogy az alkalmazás-szerepkörökhöz, ha még nem létezik.
- Amikor egy felhasználó hozzáférési csomagok hozzárendelésével lejár, hozzáférésüket törlődni fog az alkalmazásból, kivéve, ha egy másik hozzáférési csomag, amely tartalmazza az adott alkalmazás szerepkör-hozzárendelés rendelkeznek.

Egy alkalmazás kiválasztásakor az alábbiakban néhány szempontot:

- Alkalmazások hozzárendelve a szerepkörökhöz, valamint csoportok is rendelkezhetnek.  Dönthet úgy, hogy egy alkalmazás-szerepkör helyett csoport azonban egy hozzáférési csomag hozzáadása, majd az alkalmazás nem lesz látható a felhasználó a saját Access Portalon a hozzáférési csomag részeként.

1. Az a **csomag el az erőforrás-szerepkörök hozzáadása** kattintson **alkalmazások** a Select alkalmazások panel megnyitásához.

1. Válassza ki a kívánt alkalmazásokat, a hozzáférés csomaghoz.

    ![Csomag eléréséhez – erőforrás-szerepkörök – Select alkalmazások hozzáadása](./media/entitlement-management-access-package-edit/application-select.png)

1. Kattintson a **Kiválasztás** gombra.

1. Az a **szerepkör** listájához, válassza ki az alkalmazás-szerepkörhöz.

    ![Hozzáférés a csomagot,-alkalmazás erőforrás-szerepkör hozzáadása](./media/entitlement-management-access-package-edit/application-role.png)

1. Kattintson a **Hozzáadás**lehetőségre.

    Azok a felhasználók a hozzáférés csomaghoz meglévő hozzárendelések automatikusan kap hozzáférést az alkalmazáshoz, amikor felveszik.

### <a name="add-a-sharepoint-site-resource-role"></a>Adjon hozzá egy SharePoint erőforráshoz helyszerepkör

Az Azure AD automatikusan tud hozzárendelni felhasználók hozzáférés egy SharePoint Online-webhelyen vagy a SharePoint online-hoz helygyűjtemény, ha hozzá vannak rendelve egy hozzáférés-csomagot.

1. Az a **csomag el az erőforrás-szerepkörök hozzáadása** kattintson **SharePoint-webhelyek** a jelölje ki a SharePoint Online helyek panel megnyitásához.

1. Válassza ki a SharePoint Online-webhelyhez fel szeretne venni a hozzáférés-csomagban.

    ![Csomag eléréséhez – erőforrás-szerepkörök – válassza ki a SharePoint Online-webhelyek hozzáadása](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Kattintson a **Kiválasztás** gombra.

1. Az a **szerepkör** listájához, válassza ki a SharePoint online-hoz helyrendszerszerepkör.

    ![Hozzáférés a csomagot,-erőforrás szerepkör egy SharePoint Online-webhely hozzáadása](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Kattintson a **Hozzáadás**lehetőségre.

    Azok a felhasználók a hozzáférés csomaghoz meglévő hozzárendelések automatikusan kap hozzáférést a SharePoint Online helyre, amikor felveszik.

## <a name="remove-resource-roles"></a>Távolítsa el az erőforrás-szerepkörökkel

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. A bal oldali menüben kattintson a **erőforrás-szerepkörökkel**.

1. Az erőforrás-szerepkörökkel listájában keresse meg az erőforrás-szerepkör el kívánja távolítani.

1. Kattintson a három pont ( **...** ) majd **Remove erőforrás szerepkör**.

    Azok a felhasználók a hozzáférés csomaghoz meglévő hozzárendelések automatikusan lesz a hozzáférését visszavonták erőforrás ehhez a szerepkörhöz, amikor a rendszer eltávolítja.

## <a name="add-a-new-policy"></a>Új szabályzat hozzáadása

A, adja meg, akik kérhet egy hozzáférési csomagot úgy, hogy hozzon létre egy házirendet. Egyetlen hozzáférési csomag több szabályzatokat hozhat létre, ha azt szeretné, hogy a felhasználók különböző jóváhagyási és a lejárati beállítások hozzárendelések nyújtott más-más részhalmazához. Egyetlen szabályzatban csak akkor használható belső és külső felhasználók hozzárendelése ugyanaz a hozzáférés-csomag. Azonban létrehozhat két szabályzat hozzáférési egyazon csomag – egy belső felhasználók, egy, a külső felhasználók számára. Ha több szabályzat is vonatkozik egy felhasználóhoz, kérni fogja a kérésüket, hogy válassza ki a szabályzatot hozzá kell rendelni szeretnék idején.

Az alábbi ábrán egy meglévő hozzáférési csomaghoz szabályzat létrehozása magas szintű folyamata látható.

![A házirend folyamat létrehozása](./media/entitlement-management-access-package-edit/policy-process.png)

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. Kattintson a **házirendek** , majd **szabályzat hozzáadása**.

1. Írjon be egy nevet és egy leírást a szabályzathoz.

    ![Hozzon létre házirendet neve és leírása](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Az adatgyűjtésre vonatkozó felhasználói alapján **felhasználók, akik hozzáférést**, hajtsa végre a lépéseket a következő házirend-szakaszok egyikét.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Meglévő házirend szerkesztése

Szabályzatot bármikor módosíthatják. Ha módosítja a lejárati dátum, házirend, a lejárati dátum, amely már eleve a függőben lévő jóváhagyási állapot jóváhagyott kérelmek esetén nem változik.

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. Kattintson a **házirendek** és kattintson a szerkeszteni kívánt szabályzatot.

    A **szabályzat adatainak** panel megnyílik a lap alján.

    ![Access-package - házirend részletei ablaktábla](./media/entitlement-management-access-package-edit/policy-details.png)

1. Kattintson a **szerkesztése** a szabályzat szerkesztéséhez.

    ![Access-package - házirend módosítása](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Ha befejezte, kattintson a **frissítés**.

## <a name="directly-assign-a-user"></a>Közvetlenül a felhasználó hozzárendelése

Bizonyos esetekben érdemes lehet, hogy közvetlenül adott felhasználók hozzárendelése egy hozzáférés-csomagot, hogy a felhasználók nem kérhetnek a hozzáféréssel csomag a folyamatot. Felhasználók hozzárendelése közvetlenül, a hozzáférés-csomag egy szabályzatot, amely lehetővé teszi, hogy a rendszergazdai hozzárendelések közvetlen kell rendelkeznie.

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. A bal oldali menüben kattintson a **hozzárendelések**.

1. Kattintson a **új hozzárendelés** felhasználó hozzáadása a hozzáférési csomag megnyitásához.

    ![Adja hozzá a-hozzárendelések – felhasználói hozzáférés-csomaghoz](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Kattintson a **felhasználók hozzáadása** az access-csomagot a hozzárendelni kívánt felhasználók.

1. Az a **válassza ki a szabályzat** listán válassza ki a házirendet, amely rendelkezik a [None (rendszergazdai közvetlen hozzárendelések csak)](#policy-none-administrator-direct-assignments-only) beállítás.

    Ha a hozzáférés-csomag nem tartalmaz ilyen típusú szabályzat, kattintson **hozzon létre új szabályzatot** kattintva felvehet egyet.

1. Állítsa be azt szeretné, hogy a kijelölt felhasználók hozzárendelés kezdő és záró dátuma és időpontja. A befejező dátum nem áll rendelkezésre, ha a házirend-lejárati beállítások használható.

1. A közvetlen hozzárendelés rögzítésére egy indokolniuk igény szerint.

1. Kattintson a **Hozzáadás** közvetlenül hozzárendelése a kiválasztott felhasználók a hozzáférés-csomagot.

    Néhány pillanat múlva kattintson **frissítése** , tekintse meg a felhasználók a hozzárendelések listájában.

## <a name="view-who-has-an-assignment"></a>Felhasználó-hozzárendeléssel rendelkezik megtekintése

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. Kattintson a **hozzárendelések** egy aktív hozzárendelések listájának megtekintéséhez.

1. Kattintson egy adott hozzárendelés további részletek megtekintéséhez.

1. Nincs megfelelően kiépített összes erőforrás szerepkör-hozzárendelés listájának megtekintéséhez kattintson a szűrő állapota, és válassza ki **kézbesítése**.

    Megtekintéséhez további részleteket kézbesítési hiba esetén a megfelelő kérést a felhasználó megkeresése a **kérelmek** lapot.

1. Lejárt hozzárendelések megtekintéséhez kattintson a szűrő állapota, és válassza ki **lejárt**.

1. A szűrt lista CSV-fájl letöltésére, kattintson a **letöltése**.

## <a name="view-requests"></a>Kérelmek megtekintése

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. Kattintson a **kérelmek**.

1. Kattintson egy adott kérés további részletek megtekintéséhez.

## <a name="view-a-requests-delivery-errors"></a>Egy kérelem kézbesítési hibák megtekintése

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. Kattintson a **kérelmek**.

1. Válassza ki a megtekinteni kívánt kérést.

    Ha a kérés kézbesítési hibákat, a kérelem állapotának lesz **Undelivered** és a részállapot lesz **részlegesen i**.

    Ha a kérelem részletei panelen kézbesítési hibáit, szállítási hibák számát lesz.

1. Kattintson a kérés kézbesítési hibák összes száma.

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Csak lemondhatja a szolgáltatásokat, amelyek még nem lett kézbesítve függőben lévő kérelem.

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. Kattintson a **kérelmek**.

1. Kattintson a megszakítja a kérelmet

1. A kérelem részleteket tartalmazó ablaktáblán kattintson a **megszakítási kérés**.

## <a name="copy-my-access-portal-link"></a>Saját hozzáférési portál hivatkozás másolása

A címtárban szereplő legtöbb felhasználó is jelentkezzen be a saját hozzáférés-portálra, és automatikusan a igényelhetik a hozzáférési csomagok listájának megtekintéséhez. Azonban a külső üzleti partner felhasználók számára, amely még nem szerepelnek a címtárban, szüksége lesz egy hivatkozás, amely a kérelem egy hozzáférés-csomagot, hogy el őket. Mindaddig, amíg a hozzáférés csomag külső felhasználók számára érhető el, és a egy szabályzatot a külső felhasználó, a külső felhasználó használhatja a saját hozzáférési portál hivatkozása a hozzáférés csomag kéréséhez.

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. Az Áttekintés oldalon másolja az **saját hozzáférési portál hivatkozása**.

    ![Hozzáférés-csomag – áttekintés – saját hozzáférési portál hivatkozása](./media/entitlement-management-shared/my-access-portal-link.png)

1. E-mailben, vagy küldje el a hivatkozást, a külső üzleti partnerek. Azok a felhasználók a hozzáférés csomag kérése a hivatkozást megoszthatja.

## <a name="change-the-hidden-setting"></a>A rejtett beállításának módosítása

Access-csomagokat is felderíthető alapértelmezés szerint. Ez azt jelenti, hogy ha egy szabályzat lehetővé teszi a felhasználóknak a hozzáférés csomag kérelem, automatikusan jelenik meg a listában pedig a saját hozzáférés-portálon hozzáférési csomag.

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. Az Áttekintés oldalon kattintson a **szerkesztése**.

1. Állítsa be a **rejtett** beállítás.

    Ha beállítása **nem**, a hozzáférés csomag jelenik meg a felhasználó saját hozzáférési portál.

    Ha beállítása **Igen**, a hozzáférés-csomag nem szerepel a felhasználó saját hozzáférési portál. Egy felhasználó megtekintheti a hozzáférési csomag egyetlen módja, ha rendelkeznek közvetlen **saját hozzáférési portál hivatkozása** az access-csomaghoz.

## <a name="delete"></a>Törlés

Egy hozzáférés-csomag csak akkor lehet törölni, ha azt nem rendelkezik aktív felhasználói hozzárendelésekkel.

**Előfeltétel szerepkör:** Felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez** , majd nyissa meg a hozzáférés-csomagot.

1. A bal oldali menüben kattintson a **hozzárendelések** , és távolítsa el az összes felhasználó számára hozzáférést.

1. A bal oldali menüben kattintson a **áttekintése** majd **törlése**.

1. Kattintson a törlés megjelenő, **Igen**.

## <a name="when-are-changes-applied"></a>Amikor alkalmazza a módosításokat

Tagjogosultság-kezelés, az Azure ad-ben a dolgozza fel a hozzárendelési és a hozzáférési csomagok erőforrások tömeges módosítása a naponta többször. Ezért, ha a hozzárendelés, vagy módosítsa az erőforrás-szerepkörökkel, a hozzáférés csomagja, a változás az Azure AD-ben végrehajtott lehet akár 24 órát is igénybe vehet, idő plusz, ezek a módosítások, más Microsoft Online Services propagálásához szükséges vagy csatlakoztatott SaaS-alkalmazás s. Ha a módosítás néhány objektumok érinti, a módosítás valószínűleg csak percet vesz igénybe néhány a alkalmazni az Azure AD-ben utána más Azure AD-összetevők majd észlelni fogja, hogy módosítsa, és frissítse a SaaS-alkalmazásokhoz. Ha a módosítás hatással van a több ezer objektumot tartalmaz, akkor a módosítás hosszabb ideig tart. Például ha 2 alkalmazásokkal és 100 felhasználó hozzárendelését egy hozzáférési csomaggal rendelkezik, és úgy dönt, hogy a hozzáférés-csomaghoz hozzáadni egy SharePoint-hely szerepköre, valószínűleg késleltetés mindaddig, amíg az összes olyan felhasználót, hogy a SharePoint webhely szerepkör részét képezik. Az Azure AD-naplókban, az Azure ad-ben kiépítési napló és a SharePoint-webhely auditnaplók részén figyelheti.

## <a name="next-steps"></a>További lépések

- [Adjon hozzá egy katalógus tulajdonosa vagy egy hozzáférési Csomagkezelő](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Folyamat és az e-mailes értesítések kérése](entitlement-management-process.md)
