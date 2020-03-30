---
title: A csoportok adatainak frissítése a Saját alkalmazások portálon – Azure AD
description: Megtudhatja, hogy miként tekintheti meg és frissítheti a csoportokkal kapcsolatos információkat, például a saját csoportjainak megtekintését, az új csoportok létrehozását, azon csoportok megtekintését, amelyeknek már tagja, valamint a már nem részes csoportokhoz való csatlakozást.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 00639462fce4e67561b8e50609264d4423872c82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022296"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>A Csoportok adatainak frissítése a Saját alkalmazások portálon

Munkahelyi vagy iskolai fiókját a webalapú **Saját alkalmazások** portálon használhatja a szervezet felhőalapú alkalmazásainak megtekintéséhez és elindításához, a profil- és fiókadatok egy részének frissítéséhez, a **Csoportok** adatainak megtekintéséhez, valamint az alkalmazások és csoportok **hozzáférési felülvizsgálatának** elvégzéséhez. Ha nem fér hozzá a **Saját alkalmazások** portálhoz, forduljon az ügyfélszolgálathoz engedélyért.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Ha Ön rendszergazda, a felhőalapú alkalmazások beállításával és kezelésével kapcsolatos további információkért az [Alkalmazáskezelési dokumentációban](https://docs.microsoft.com/azure/active-directory/manage-apps)talál.

## <a name="view-your-groups-information"></a>A csoportok adatainak megtekintése

Ha a rendszergazda engedélyt adott a **Csoportok** csempe megtekintésére, a következőket teheti:

- **Csoporttagként.** Részletek megtekintése vagy kilépés bármely csoportból.

- **Csoporttulajdonosként.** Megtekintheti a részleteket, új csoportot hozhat létre, tagokat vehet fel vagy távolíthat el, illetve törölheti a csoportot.

### <a name="to-view-your-groups-information"></a>A csoportadatok megtekintése

1. Jelentkezzen be munkahelyi vagy iskolai fiókjába.

2. Nyissa meg a webböngészőt, és nyissa meg a https://myapps.microsoft.com, vagy használja a szervezet által megadott hivatkozást. Előfordulhat például, hogy a rendszer a szervezet testreszabott https://myapps.microsoft.com/contoso.comoldalára irányítja, például .

    Megjelenik **az Alkalmazások** lap, amely a szervezet tulajdonában lévő és az Ön számára használható összes felhőalapú alkalmazást jeleníti meg.

    ![Alkalmazások lap a Saját alkalmazások portálon](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. A **csoportokkal** kapcsolatos információk megtekintéséhez válassza a Csoportok csempét.

    ![Csoportok lap a saját és a tagcsoportokkal is](media/my-apps-portal/my-apps-portal-groups-page.png)

4. Az engedélyek alapján a Csoportok lapon a következőkre **használhatja:**

    - **Tekintse át a saját csoportjait.** A szervezeten belüli csoportok adatait az **általam birtokolt csoportok** területéről tekintheti meg. Adott csoportnév kiválasztása további részleteket tartalmaz a csoportról, beleértve a csoport típusát, a tagok számát, az illesztési házirendet és az aktív tagok listáját.

    - **Hozzon létre egy új csoportot.** Hozzon létre egy új csoportot veled, mint a tulajdonos a **csoportok én tulajdonában** területen. A konkrét lépéseket a cikk [Új csoport létrehozása](#create-a-new-group) című szakaszában olvashat.

    - **Meglévő csoport szerkesztése.** A saját csoportok részleteinek szerkesztése. A konkrét lépéseket a cikk [meglévő csoport szerkesztése](#edit-an-existing-group) című szakaszában olvashatja.

    - **Tagok hozzáadása vagy eltávolítása.** Tagok hozzáadása vagy eltávolítása a saját csoportjaihoz. A konkrét lépéseket a cikk [taghozzáadása vagy eltávolítása](#add-or-remove-a-member) című szakaszában olvashatja.

    - **Office 365-csoport megújítása.** Ha szervezete engedélyezi, megújíthatja az Office 365-csoportokat. További lépéseket a cikk [Office 365-ös csoport megújítása](#renew-an-office-365-group) című szakaszában olvashat. 

    - **Csoport törlése.** Törölje a saját csoportjait. A konkrét lépéseket a cikk [Csoport törlése](#delete-a-group) című szakaszában olvashat.

    - **Tekintse át azon csoportokat, amelyeknek tagja.** Megtekintheti azoknak a csoportoknak a nevét, amelyeknek tagja a **csoportcsoportok** nak. Adott csoportnév kiválasztása további részleteket tartalmaz a csoportról, beleértve a csoport típusát, a tagok számát, az illesztési házirendet és az aktív tagok listáját.

    - **Csatlakozzon egy csoporthoz.** Csatlakozzon egy meglévő csoporthoz, amelynek még nem tagja, a **csoportból, amelynek a területen vagyok.** A konkrét lépéseket a [Csatlakozás meglévő csoporthoz](#join-an-existing-group)című témakörben található.

## <a name="create-a-new-group"></a>Új csoport létrehozása

1. A **Csoportok** lapon válassza a **Csoport létrehozása** az **Általam birtokolt csoportok** területéről lehetőséget.

    Megjelenik **a Csoport létrehozása** mező.

    ![Csoportlétrehozása párbeszédpanel](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Adja meg a szükséges adatokat:

    - **Csoport típusa:**

        - **Biztonsági.** Segítségével kezelheti a tag és a számítógép hozzáféréseket a megosztott erőforrásokhoz a felhasználók egy csoportjára. Például létrehozhat egy biztonsági csoportot egy meghatározott biztonsági házirendhez. Ha így tesz, az összes tagnak egyszerre oszthatja ki az engedélyeket, nem kell az engedélyeket minden tagnak külön kiadni.

        - **Office 365- ös office 365-** Együttműködési lehetőségeket biztosít a tagok számára rendelkezésre bocsátott megosztott postaládába, naptár, fájlok, SharePoint-webhelyre és sok egyéb révén. Ez a beállítás lehetővé teszi a szervezetnél kívüli személyek hozzáadását is a csoporthoz.

    - **Csoport neve.** Olyan nevet adjon a csoportnak, aminek értelme van és nem felejti el.

    - **Csoportleírása (nem kötelező).** Ha szeretne kiegészítő leírást adni a csoporthoz.

    - **Csoportházirend.** Válassza azt, hogy engedélyezheti mindenkinek, hogy csatlakozzon a csoporthoz, vagy csak a csoport tulajdonosának engedélyezze a tagok hozzáadását.

3. Kattintson a **Létrehozás** gombra.

    Az új csoport jön létre veled, mint a tulajdonos, és úgy tűnik, a **csoportok saját** listán. Mivel te vagy a tulajdonos, ez a csoport is megjelenik a **Csoportok vagyok a** listán.

## <a name="edit-an-existing-group"></a>Meglévő csoport szerkesztése

A csoport létrehozása után módosíthatja annak részleteit, beleértve a meglévő adatok frissítését is.

1. Jelölje ki a szerkesztni kívánt csoportot a **Csoportok** lapon, majd válassza a **Részletek szerkesztése** lehetőséget a * &lt;group_name&gt; * lapon.

    Megjelenik **a Részletek szerkesztése** mező, és frissítheti a csoport létrehozásakor hozzáadott adatokat.

2. Hajtsa végre az összes módosítást, majd válassza a **Frissítés lehetőséget.**

## <a name="add-or-remove-a-member"></a>Tag hozzáadása vagy eltávolítása

A saját csoporthoz tagokat vehet fel vagy távolíthat el.

1. Jelölje ki azt a csoportot, amelyhez **+** tagokat szeretne hozzáadni, majd jelölje ki a * &lt;group_name&gt; * lapon.

    ![Csoporttag hozzáadása a + jellel kiemelve](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Keresse meg a hozzáadni kívánt tagot a **Tagok hozzáadása** mezőben, és válassza a **Hozzáadás lehetőséget.**

    ![Tagok hozzáadása mező, új taggal](media/my-apps-portal/my-apps-portal-add-member-page.png)

    A rendszer meghívót küld az új tagnak, hogy megkezdhesse a szervezet alkalmazásainak elérését.

3. Ha véletlenül adott hozzá egy tagot, vagy ha egy tag elhagyta a szervezetet, eltávolíthatja a tagot, ha a * &lt;&gt; group_name* lapon a tag neve melletti **Tag eltávolítása** lehetőséget választja.

    ![Tag eltávolítása a kiemelt eltávolítási hivatkozással](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Office 365-ös csoport megújítása

Ha szervezete engedélyezi, megújíthat egy Office 365-csoportot, meghosszabbítva a lejárati dátumot.

1. Jelölje ki a megújítani kívánt Office 365-csoportot, majd válassza **a Megújítás csoport**lehetőséget.

    ![Office 365-csoport megújítása a lejárati dátum meghosszabbítása](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. A megerősítő üzenet bezárásához kattintson az **OK** gombra.

    A lap frissítése után megjelenik a frissített **utolsó megújított** és **a csoport lejárati** dátuma.

## <a name="delete-a-group"></a>Csoport törlése

A saját csoportjai közül bármelyiket bármikor törölheti. Ha azonban véletlenül töröl egy csoportot, létre kell hoznia, és újra tagokat kell hozzáadnia.

1. Jelölje ki a véglegesen törölni kívánt csoportot, * &lt;majd&gt; * válassza a group_name lap **Delete csoport csoportjának** jelölését.

    ![<Group_name> lap, amelyen a Csoport törlése hivatkozás van kiemelve](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. A megerősítő üzenetben válassza az **Igen** lehetőséget.

    A csoport véglegesen törlődik.

## <a name="join-an-existing-group"></a>Csatlakozás meglévő csoporthoz

A **Csoportok** lapon csatlakozhat egy már meglévő csoporthoz, vagy kiléphet onnan.

1. A **Csoportok** lapon válassza az **Csatlakozzanak csoportot** a területen **lévő Csoportok** között.

    Megjelenik **az Illesztés csoportok** lap.

    ![Csatlakozás csoportokhoz lap, kiemelve a Csatlakozás csoport gombbal](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Az **Illesztés, csoportok** lapon jelölje ki annak a csoportnak a nevét, amelyhez csatlakozni szeretne, tekintse meg a társított csoport adatait, majd ha a csoport elérhető, válassza a **Join group (Csatlakozás csoport) lehetőséget.**

    Ha a csoport megköveteli, hogy a csoport tulajdonosa jóváhagyja a tagságot, meg kell adnia egy üzleti indoklást, hogy miért kell csatlakoznia a csoporthoz, majd válassza a **Kérés lehetőséget.** Ha a csoportnak nincs szüksége jóváhagyásra, akkor azonnal hozzáadódik tagként, és a csoport megjelenik a **Csoportok i vagyok a** listán.

3. Ha véletlenül csatlakozott egy csoporthoz, vagy ha már nem kell részt vennie benne, kiválaszthatja a csoport nevét az **Illesztés csoportok** lapon, majd válassza a **Kilépés csoportból**lehetőséget.

    ![Csatlakozás csoportokhoz lap, kiemelve a Csoport elhagyása gombbal](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>További lépések

- [Alkalmazások elérése és használata a Saját alkalmazások portálon.](my-apps-portal-end-user-access.md)

- [Módosítsa a profiladatait.](my-apps-portal-end-user-update-profile.md)

- [Végezze el saját hozzáférési véleményeit.](my-apps-portal-end-user-access-reviews.md)
