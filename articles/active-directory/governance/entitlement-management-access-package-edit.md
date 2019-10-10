---
title: Meglévő hozzáférési csomag szerkesztése és kezelése az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogyan szerkesztheti és kezelheti a meglévő hozzáférési csomagokat a Azure Active Directory jogosultságok kezelése (előzetes verzió) szolgáltatásban.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/26/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 864a6476ad894952a2ef877d534ffe8a86e07819
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170029"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Meglévő hozzáférési csomag szerkesztése és kezelése az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Egy hozzáférési csomag lehetővé teszi az erőforrások és házirendek egyszeri beállítását, amely automatikusan felügyeli a hozzáférést a hozzáférési csomag élettartamához. Hozzáférés-csomagkezelőként bármikor módosíthatja egy hozzáférési csomag erőforrásait anélkül, hogy aggódnia kellene a felhasználó új erőforrásaihoz való hozzáférésének kiosztásával vagy az előző erőforrásokból való hozzáférés eltávolításával. A szabályzatok bármikor frissíthetők, azonban a házirend módosításai csak az új hozzáféréseket érintik.

Ez a cikk a meglévő hozzáférési csomagok szerkesztését és kezelését ismerteti.

## <a name="add-resource-roles"></a>Erőforrás-Szerepkörök hozzáadása

Az erőforrás-szerepkörök az erőforrásokhoz társított engedélyek gyűjteményei. Ahhoz, hogy a felhasználók számára elérhetővé tegye az erőforrásokat, erőforrás-szerepköröket adhat hozzá a hozzáférési csomaghoz. Hozzáadhat erőforrás-szerepköröket a csoportokhoz, a csoportokhoz, az alkalmazásokhoz és a SharePoint-webhelyekhez.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson az **erőforrás-szerepkörök**elemre.

1. Kattintson az **erőforrás-Szerepkörök hozzáadása** elemre, hogy megnyissa az erőforrás-Szerepkörök hozzáadása a csomaghoz lapot.

    ![Hozzáférési csomag – erőforrás-Szerepkörök hozzáadása](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. Attól függően, hogy szeretne-e csoportot, csapatot, alkalmazást vagy SharePoint-webhelyet felvenni, hajtsa végre a következő erőforrás-szerepköri szakaszban található lépéseket.

### <a name="add-a-group-or-team-resource-role"></a>Csoport vagy csoport erőforrás-szerepkör hozzáadása

A jogosultságok kezelésével automatikusan hozzáadhat felhasználókat egy csoporthoz vagy egy Microsoft-csapathoz, amikor hozzáférési csomagot rendelnek hozzájuk. 

- Ha egy csoport vagy csapat egy hozzáférési csomag része, és egy felhasználó hozzá van rendelve az adott hozzáférési csomaghoz, akkor a felhasználó hozzá lesz adva az adott csoporthoz vagy csapathoz, ha még nem létezik.
- Ha egy felhasználó hozzáférési csomagjának hozzárendelése lejár, a rendszer eltávolítja azokat a csoportból vagy csapatból, kivéve, ha jelenleg egy másik hozzáférési csomagra van rendelve, amely ugyanazt a csoportot vagy csapatot tartalmazza.

Bármelyik [Azure ad biztonsági csoportot vagy Office 365 csoportot](../fundamentals/active-directory-groups-create-azure-portal.md)kiválaszthatja.  A rendszergazdák hozzáadhatnak bármely csoportot egy katalógushoz; a katalógus tulajdonosai bármelyik csoportot hozzáadhatják a katalógushoz, ha a csoport tulajdonosai. A csoportok kiválasztásakor tartsa szem előtt az alábbi Azure AD-korlátozásokat:

- Ha egy felhasználó, a vendéget is beleértve, tagként egy csoport vagy csapat tagjaként van hozzáadva, akkor az adott csoport vagy csapat többi tagját is láthatják.
- Az Azure AD nem tudja módosítani a Windows Server Active Directoryról szinkronizált csoport tagságát Azure AD Connect használatával, vagy az Exchange Online-ban, terjesztési csoportban létrehozottként.  
- A dinamikus csoportok tagsága nem frissíthető egy tag hozzáadásával vagy eltávolításával, így a dinamikus csoporttagságok nem alkalmasak a jogosultsági felügyelettel való használatra.

További információt a csoportok és az [Office 365-csoportok és a Microsoft Teams](/microsoftteams/office-365-groups) [összevetése](/office365/admin/create-groups/compare-groups) című témakörben talál.

1. Az **erőforrás-Szerepkörök hozzáadása a csomaghoz** lapon kattintson a **csoportok és** csoportok elemre a csoportok kiválasztása panel megnyitásához.

1. Válassza ki azokat a csoportokat és csapatokat, amelyeket bele szeretne foglalni a hozzáférési csomagba.

    ![Hozzáférési csomag – erőforrás-Szerepkörök hozzáadása – csoportok kiválasztása](./media/entitlement-management-access-package-edit/group-select.png)

1. Kattintson a **Kiválasztás** gombra.

    Miután kiválasztotta a csoportot vagy a csapatot, az **altípus** oszlopban a következő altípusok egyike jelenik meg:

    |  |  |
    | --- | --- |
    | Biztonság | Erőforrásokhoz való hozzáférés biztosítására szolgál. |
    | Terjesztés | Az értesítések egy csoportba való küldésére szolgál. |
    | O365 | Az Office 365-csoport, amely nem engedélyezett csapatok. A felhasználók közötti, a vállalaton belüli és kívüli együttműködéshez használatos. |
    | Team | Az Office 365-csoport, amelyeken engedélyezve van a csapatok. A felhasználók közötti, a vállalaton belüli és kívüli együttműködéshez használatos. |

1. A **szerepkör** listából válassza a **tulajdonos** vagy a **tag**elemet.

    Általában kiválasztja a tag szerepkört. Ha a tulajdonos szerepkört választja, a felhasználók más tagokat vagy tulajdonosokat is hozzáadhatnak vagy eltávolíthatnak.

    ![Hozzáférési csomag – erőforrás-szerepkör hozzáadása csoporthoz vagy csapathoz](./media/entitlement-management-access-package-edit/group-role.png)

1. Kattintson a **Hozzáadás** parancsra.

    A hozzáférési csomaghoz meglévő hozzárendelésekkel rendelkező felhasználók automatikusan ennek a csoportnak vagy csapatnak a tagjai lesznek, amikor hozzáadják őket.

### <a name="add-an-application-resource-role"></a>Alkalmazás-erőforrás szerepkör hozzáadása

Az Azure AD automatikusan hozzárendelheti a felhasználókat egy Azure AD vállalati alkalmazáshoz, beleértve az SaaS-alkalmazásokat és a szervezet Azure AD-ba összevont alkalmazásait is, amikor egy felhasználó hozzáférési csomagot kap. Az összevont egyszeri bejelentkezésen keresztül az Azure AD-vel integrált alkalmazások esetében az Azure AD összevonási jogkivonatokat állít ki az alkalmazáshoz hozzárendelt felhasználók számára.

Az alkalmazások több szerepkörrel rendelkezhetnek. Ha egy alkalmazás egy hozzáférési csomaghoz van hozzáadva, ha az alkalmazásnak egynél több szerepköre van, meg kell adnia a megfelelő szerepkört a felhasználók számára.  Ha alkalmazásokat fejleszt, többet is megtudhat arról, hogyan biztosíthatja ezeket a szerepköröket az alkalmazásaihoz az [SAML-jogkivonatban kiadott szerepkör-jogcím konfigurálásának](../develop/active-directory-enterprise-app-role-management.md)módjában.

Ha egy alkalmazás-szerepkör egy hozzáférési csomag része:

- Ha egy felhasználó hozzá van rendelve a hozzáférési csomaghoz, a felhasználó hozzá lesz adva az adott alkalmazási szerepkörhöz, ha még nem létezik.
- Ha a felhasználó hozzáférési csomagjának hozzárendelése lejár, a hozzáférésük el lesz távolítva az alkalmazásból, kivéve, ha az adott alkalmazási szerepkört tartalmazó másik hozzáférési csomaghoz vannak hozzárendelve.

Az alkalmazások kiválasztásakor a következő szempontokat érdemes figyelembe venni:

- Az alkalmazások is rendelkezhetnek a szerepkörökhöz hozzárendelt csoportokkal is.  Lehetőség van arra is, hogy egy hozzáférési csomagban vegyen fel egy csoportot egy alkalmazási szerepkör helyett, de az alkalmazás nem lesz látható a felhasználó számára a hozzáférési portál hozzáférési csomagjának részeként.

1. Az **erőforrás-Szerepkörök hozzáadása a csomaghoz** lapon kattintson az **alkalmazások** elemre az alkalmazások kiválasztása panel megnyitásához.

1. Válassza ki a hozzáférési csomagban szerepeltetni kívánt alkalmazásokat.

    ![Hozzáférési csomag – erőforrás-Szerepkörök hozzáadása – alkalmazások kiválasztása](./media/entitlement-management-access-package-edit/application-select.png)

1. Kattintson a **Kiválasztás** gombra.

1. A **szerepkör** listából válassza ki az alkalmazás szerepkört.

    ![Hozzáférési csomag – erőforrás-szerepkör hozzáadása egy alkalmazáshoz](./media/entitlement-management-access-package-edit/application-role.png)

1. Kattintson a **Hozzáadás** parancsra.

    A hozzáférési csomaghoz meglévő hozzárendelésekkel rendelkező felhasználók automatikusan hozzáférhetnek ehhez az alkalmazáshoz, amikor az hozzá van adva.

### <a name="add-a-sharepoint-site-resource-role"></a>SharePoint-webhely erőforrás-szerepkörének hozzáadása

Az Azure AD automatikusan hozzá tud rendelni felhasználókat a SharePoint Online-webhelyekhez vagy a SharePoint Online-webhelycsoportok eléréséhez, ha hozzáférési csomagot rendelnek hozzájuk.

1. Az **erőforrás-szerepkörök felvétele a csomagba** lapon kattintson a **SharePoint-helyek** elemre a SharePoint Online-webhelyek kiválasztása panel megnyitásához.

1. Válassza ki azokat a SharePoint Online-webhelyeket, amelyeket fel szeretne venni a hozzáférési csomagba.

    ![Hozzáférési csomag – erőforrás-Szerepkörök hozzáadása – SharePoint Online-webhelyek kiválasztása](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Kattintson a **Kiválasztás** gombra.

1. A **szerepkör** listából válassza ki a SharePoint Online-webhely szerepkört.

    ![Hozzáférési csomag – erőforrás-szerepkör hozzáadása egy SharePoint Online-webhelyhez](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Kattintson a **Hozzáadás** parancsra.

    A hozzáférési csomaghoz meglévő hozzárendelésekkel rendelkező felhasználók automatikusan hozzáférhetnek ehhez a SharePoint Online-webhelyhez, amikor az hozzá van adva.

## <a name="remove-resource-roles"></a>Erőforrás-szerepkörök eltávolítása

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson az **erőforrás-szerepkörök**elemre.

1. Az erőforrás-szerepkörök listájában keresse meg az eltávolítani kívánt erőforrás-szerepkört.

1. Kattintson a három pontra ( **..** .), majd az **erőforrás-szerepkör eltávolítása**elemre.

    A hozzáférési csomaghoz meglévő hozzárendelésekkel rendelkező felhasználók számára a rendszer automatikusan visszavonja a hozzáférését erre az erőforrás-szerepkörre, ha az eltávolításra kerül.

## <a name="add-a-new-policy"></a>Új szabályzat hozzáadása

A hozzáférési csomag igénylésének megadásának módja egy szabályzat létrehozása. Egyetlen hozzáférési csomaghoz több szabályzat is létrehozható, ha engedélyezni szeretné, hogy a különböző felhasználók a különböző jóváhagyási és lejárati beállításokkal rendelkező hozzárendeléseket kapjanak. Egyetlen házirend nem használható belső és külső felhasználók ugyanahhoz a hozzáférési csomaghoz való hozzárendeléséhez. Ugyanakkor két házirendet is létrehozhat ugyanabban a hozzáférési csomagban – egyet a belső felhasználókhoz, egyet pedig a külső felhasználókhoz. Ha több házirend is érvényes a felhasználóra, a rendszer a kérésük időpontjában kérni fogja, hogy válassza ki azt a szabályzatot, amelyet hozzá szeretne rendelni.

Az alábbi ábrán egy meglévő hozzáférési csomagra vonatkozó házirend létrehozásának magas szintű folyamata látható.

![Házirend-folyamat létrehozása](./media/entitlement-management-access-package-edit/policy-process.png)

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **házirendek** , majd a **házirend hozzáadása**elemre.

1. Adja meg a szabályzat nevét és leírását.

    ![Szabályzat létrehozása névvel és leírással](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. A **hozzáférést kérő felhasználók**kiválasztása alapján hajtsa végre a következő szabályzatok egyikének lépéseit.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Meglévő szabályzat szerkesztése

Bármikor szerkesztheti a szabályzatokat. Ha módosítja egy házirend lejárati dátumát, a függőben lévő jóváhagyás vagy jóváhagyott állapotú kérelmek lejárati dátuma nem változik.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **házirendek** elemre, majd kattintson a szerkeszteni kívánt szabályzatra.

    Megnyílik a **szabályzat részletei** ablaktábla az oldal alján.

    ![Hozzáférési csomag – szabályzat részletei ablaktábla](./media/entitlement-management-access-package-edit/policy-details.png)

1. A szabályzat szerkesztéséhez kattintson a **Szerkesztés** gombra.

    ![Hozzáférési csomag – szabályzat szerkesztése](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Ha elkészült, kattintson a **frissítés**gombra.

## <a name="directly-assign-a-user"></a>Felhasználó közvetlen kiosztása

Bizonyos esetekben előfordulhat, hogy közvetlenül egy adott felhasználót szeretne hozzárendelni egy hozzáférési csomaghoz, hogy a felhasználóknak ne kelljen átesniük a hozzáférési csomag igénylésének folyamatán. A felhasználók közvetlen hozzárendeléséhez a hozzáférési csomagnak rendelkeznie kell egy olyan házirenddel, amely lehetővé teszi a közvetlen rendszergazdai hozzárendeléseket.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson a **hozzárendelések**elemre.

1. Kattintson az **új hozzárendelés** lehetőségre a felhasználó hozzáadása a csomaghoz lehetőség megnyitásához.

    ![Hozzárendelések – felhasználó hozzáadása a csomag eléréséhez](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Kattintson a **felhasználók hozzáadása** elemre azon felhasználók kiválasztásához, akiknek a hozzáférési csomagot hozzá szeretné rendelni.

1. A **házirend kiválasztása** listából válassza ki azt a szabályzatot, amelynek [nincs (csak a rendszergazda közvetlen hozzárendelései)](#policy-none-administrator-direct-assignments-only) beállítása.

    Ha ez a hozzáférési csomag nem rendelkezik ilyen típusú házirenddel, kattintson az **új házirend létrehozása** lehetőségre, ha hozzá szeretne adni egyet.

1. Adja meg azt a dátumot és időpontot, ameddig a kiválasztott felhasználók hozzárendelésének kezdési és befejezési időpontja. Ha nincs megadva záró dátum, a rendszer a szabályzat lejárati beállításait fogja használni.

1. Igény szerint megadhatja a közvetlen hozzárendelését a rekordok megőrzése érdekében.

1. A **Hozzáadás** gombra kattintva közvetlenül hozzárendelheti a kiválasztott felhasználókat a hozzáférési csomaghoz.

    Néhány pillanat elteltével kattintson a **frissítés** gombra, hogy megjelenjenek a felhasználók a hozzárendelések listájában.

## <a name="view-who-has-an-assignment"></a>Hozzárendelést használó nézet megtekintése

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **hozzárendelések** elemre az aktív hozzárendelések listájának megtekintéséhez.

1. A további részletek megtekintéséhez kattintson egy konkrét hozzárendelésre.

1. Ha meg szeretné tekinteni azoknak a hozzárendeléseknek a listáját, amelyeken nincs megfelelően kiépítve az összes erőforrás-szerepkör, kattintson a szűrő állapotára, és válassza a **kézbesítés**lehetőséget.

    A kézbesítési hibákkal kapcsolatos további részletekért tekintse meg a felhasználó megfelelő kérését a **kérelmek** lapon.

1. A lejárt hozzárendelések megjelenítéséhez kattintson a szűrő állapotára, és válassza a **lejárt**lehetőséget.

1. A szűrt lista CSV-fájljának letöltéséhez kattintson a **Letöltés**gombra.

## <a name="view-requests"></a>Kérelmek megtekintése

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek**elemre.

1. A további részletek megtekintéséhez kattintson egy adott kérésre.

## <a name="view-a-requests-delivery-errors"></a>Kérelem kézbesítési hibáinak megtekintése

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek**elemre.

1. Válassza ki a megtekinteni kívánt kérelmet.

    Ha a kérelem kézbesítési hibával rendelkezik, a rendszer nem **kézbesíti** a kérés állapotát, és a rendszer **részlegesen kézbesíti**az alállapotot.

    Ha kézbesítési hibák merülnek fel, a kérelem részleteit tartalmazó ablaktáblán a kézbesítési hibák száma jelenik meg.

1. Kattintson a számra az összes kérelem kézbesítési hibájának megjelenítéséhez.

## <a name="cancel-a-pending-request"></a>Függőben lévő kérelem megszakítása

Csak olyan függőben lévő kérést lehet megszakítani, amely még nem lett leküldve.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **kérelmek**elemre.

1. Kattintson a megszakítani kívánt kérelemre

1. A kérelem részletei ablaktáblán kattintson a **kérelem megszakítása**elemre.

## <a name="copy-my-access-portal-link"></a>Hozzáférési portál hivatkozásának másolása

A címtár legtöbb felhasználója bejelentkezhet a saját hozzáférési portálra, és automatikusan megtekintheti az általa kérhető hozzáférési csomagok listáját. Ha azonban a külső üzleti partnerek olyan felhasználói, akik még nem szerepelnek a címtárban, el kell küldeni nekik egy hivatkozást, amely a hozzáférési csomag igénylésére használható. 

Fontos, hogy átmásolja a teljes saját hozzáférési portál hivatkozást, amikor egy belső üzleti partnernek küldi el. Ezzel biztosíthatja, hogy a partner hozzáférjen a címtár portálához a kérelem elvégzéséhez. 

A hivatkozás a "myaccess" kifejezéssel kezdődik, tartalmaz egy könyvtári hivatkozást, és befejezi a hozzáférési csomag AZONOSÍTÓját. Győződjön meg arról, hogy a hivatkozás a következők mindegyikét tartalmazza:

 `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

Ha a hozzáférési csomag engedélyezve van a külső felhasználók számára, és a külső felhasználó címtárának van szabályzata, a külső felhasználó a saját hozzáférési portál hivatkozásra kattintva kérheti le a hozzáférési csomagot.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Az Áttekintés lapon másolja a **saját hozzáférési portál hivatkozást**.

    ![Hozzáférési csomag áttekintése – saját hozzáférési portál hivatkozása](./media/entitlement-management-shared/my-access-portal-link.png)

1. Küldjön e-mailt, vagy küldje el a külső üzleti partnerének mutató hivatkozást. Megoszthatják a hivatkozást a felhasználókkal a hozzáférési csomag igényléséhez.

## <a name="change-the-hidden-setting"></a>A rejtett beállítás módosítása

A hozzáférési csomagok alapértelmezés szerint felderíthetők. Ez azt jelenti, hogy ha egy házirend lehetővé teszi a felhasználó számára a hozzáférési csomag kérését, automatikusan a saját hozzáférési portálon megjelenő hozzáférési csomagot fogják látni.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Az Áttekintés lapon kattintson a **Szerkesztés**elemre.

1. Állítsa be a **rejtett** beállítást.

    Ha a **nem**értékre van állítva, a hozzáférési csomag a felhasználó saját hozzáférési portálján jelenik meg.

    Ha az **Igen**értékre van állítva, a hozzáférési csomag nem jelenik meg a felhasználó saját hozzáférési portálján. Az egyetlen mód, amikor a felhasználó megtekintheti a hozzáférési csomagot, ha a közvetlen hozzáférési portál a hozzáférési csomagra **mutató hivatkozást** tartalmaz.

## <a name="delete"></a>Törlés

Hozzáférési csomag csak akkor törölhető, ha nem rendelkezik aktív felhasználói hozzárendelésekkel.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson a **hozzárendelések** elemre, és távolítsa el az összes felhasználó hozzáférését.

1. A bal oldali menüben kattintson az **Áttekintés** , majd a **Törlés**elemre.

1. A megjelenő törlési üzenetben kattintson az **Igen**gombra.

## <a name="when-are-changes-applied"></a>A módosítások alkalmazása esetén

A jogosultságok kezelése során az Azure AD naponta többször dolgozza fel a hozzáférési csomagok hozzárendeléseinek és erőforrásainak tömeges módosításait. Így ha hozzárendelést végez, vagy megváltoztatja a hozzáférési csomag erőforrás-szerepköreit, akár 24 órát is igénybe vehet az Azure AD-ben, valamint azt, hogy mennyi időt vesz igénybe a módosítások más Microsoft Online szolgáltatásokba vagy csatlakoztatott SaaS-alkalmazásba való propagálása. s. Ha a változás csupán néhány objektumot érint, a változás valószínűleg csak néhány percet vesz igénybe az Azure AD-ben, amely után a többi Azure AD-összetevő ezt követően észleli az SaaS-alkalmazások módosítását és frissítését. Ha a változás több ezer objektumot érint, akkor a módosítás hosszabb ideig tart. Ha például egy 2 alkalmazásból és egy 100 felhasználói hozzárendelésből álló hozzáférési csomaggal rendelkezik, és úgy dönt, hogy egy SharePoint-webhely szerepkört ad hozzá a hozzáférési csomaghoz, akkor előfordulhat, hogy az összes felhasználó a SharePoint-webhely szerepkörbe való belefoglalása után késésben van. Nyomon követheti az előrehaladást az Azure AD-naplóban, az Azure AD-kiépítési naplóban és a SharePoint-webhely naplófájljaiban.

## <a name="next-steps"></a>Következő lépések

- [Kérelmek feldolgozása és e-mail-értesítések](entitlement-management-process.md)
