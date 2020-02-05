---
title: A csoportok adatainak frissítése a saját alkalmazások portálján – Azure AD
description: Megtudhatja, hogyan tekintheti meg és frissítheti a csoportokkal kapcsolatos információkat, például megtekintheti a saját csoportokat, új csoportokat hozhat létre, megtekintheti azokat a csoportokat, amelyekhez már tagja van, és csatlakozhat a már nem részét képező csoportokhoz.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022296"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>A csoportok adatainak frissítése a saját alkalmazások portálon

Használhatja munkahelyi vagy iskolai fiókját a webalapú **My apps** portálon, hogy megtekintse és elindítsa a szervezete felhőalapú alkalmazásait, a profil-és fiókadatok frissítését, a **csoportok** adatainak megtekintését, valamint az alkalmazások és csoportok **hozzáférési felülvizsgálatának** elvégzését. Ha nincs hozzáférése a **saját alkalmazások** portálhoz, akkor engedélyt kell adnia az ügyfélszolgálatnak.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Ha Ön rendszergazda, további információt talál arról, hogyan állíthatja be és kezelheti felhőalapú alkalmazásait az [alkalmazás-felügyeleti dokumentációban](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>A csoportok adatainak megtekintése

Ha a rendszergazda engedélyt kapott a **csoportok** csempék megtekintésére, a következőket teheti:

- **Csoport tagjaként.** Tekintse meg a részleteket, vagy hagyjon egy csoportot.

- **Csoport tulajdonosaként.** Tekintse meg a részleteket, hozzon létre egy új csoportot, adjon hozzá vagy távolítson el tagokat, vagy törölje a csoportot.

### <a name="to-view-your-groups-information"></a>A csoportok adatainak megtekintése

1. Jelentkezzen be a munkahelyi vagy iskolai fiókjába.

2. Nyissa meg a webböngészőt, és lépjen a https://myapps.microsoft.com ra, vagy használja a szervezete által biztosított hivatkozást. Előfordulhat például, hogy a szervezete testreszabott lapjára irányítja át, például https://myapps.microsoft.com/contoso.com.

    Megjelenik az **alkalmazások** lap, amely megjeleníti a szervezete tulajdonában lévő összes felhőalapú alkalmazást, és elérhető a használatra.

    ![Alkalmazások lap a saját alkalmazások portálon](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. A csoporttal kapcsolatos információk megtekintéséhez válassza a **csoportok** csempét.

    ![Csoportok lap a tulajdonos és a tagok csoportokkal](media/my-apps-portal/my-apps-portal-groups-page.png)

4. Az engedélyei alapján a **csoportok** lapon a következőket végezheti el:

    - **Tekintse át a saját csoportokat.** Megtekintheti a szervezeten belül a **saját területén lévő csoportok adatait** . Egy adott csoportnév kiválasztásával további részleteket talál a csoportról, beleértve a csoport típusát, a tagok számát, a csatlakozási szabályzatot és az aktív tagok listáját.

    - **Hozzon létre egy új csoportot.** Hozzon létre egy új csoportot a tulajdonosként a **saját csoportok** részen. Adott lépésekért tekintse meg a jelen cikk [új csoport létrehozása](#create-a-new-group) című szakaszát.

    - **Meglévő csoport szerkesztése.** Szerkessze a saját csoportjai részleteit. Adott lépésekért tekintse meg a jelen cikk [meglévő csoport szerkesztése](#edit-an-existing-group) című szakaszát.

    - **Tagok hozzáadása vagy eltávolítása.** Tagok hozzáadása vagy eltávolítása a saját csoportokhoz. Adott lépésekért tekintse meg a jelen cikk [tag hozzáadása vagy eltávolítása](#add-or-remove-a-member) című szakaszát.

    - **Office 365-csoport megújítása.** Ha a szervezete engedélyezi, megújíthatja az Office 365-csoportokat. Adott lépésekért tekintse meg a jelen cikk [Office 365-csoport megújítása](#renew-an-office-365-group) című szakaszát. 

    - **Csoport törlése.** Törölje az Ön tulajdonában lévő összes csoportot. Adott lépésekért tekintse meg a jelen cikk [csoport törlése](#delete-a-group) című szakaszát.

    - **Tekintse át azokat a csoportokat, amelyeknek Ön a része.** Megtekintheti azoknak a csoportoknak a nevét, amelyeknek Ön a tagja a **csoportban található csoportoknak** . Egy adott csoportnév kiválasztásával további részleteket talál a csoportról, beleértve a csoport típusát, a tagok számát, a csatlakozási szabályzatot és az aktív tagok listáját.

    - **Csatlakozás egy csoporthoz.** Csatlakozzon egy meglévő csoporthoz, amelyhez még nem tagja, a csoportok területen található **csoportokból** . Adott lépésekért tekintse meg a [meglévő csoport csatlakoztatása](#join-an-existing-group)című témakört.

## <a name="create-a-new-group"></a>Új csoport létrehozása

1. A **csoportok** lapon válassza a **csoport létrehozása** lehetőséget a **saját** csoport területen.

    Megjelenik a **csoport létrehozása** mező.

    ![Csoport létrehozása](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Adja meg a szükséges adatokat:

    - **Csoport típusa:**

        - **Biztonság.** Segítségével kezelheti a tag és a számítógép hozzáféréseket a megosztott erőforrásokhoz a felhasználók egy csoportjára. Például létrehozhat egy biztonsági csoportot egy meghatározott biztonsági házirendhez. Ha így tesz, az összes tagnak egyszerre oszthatja ki az engedélyeket, nem kell az engedélyeket minden tagnak külön kiadni.

        - **Office 365.** Együttműködési lehetőségeket biztosít a tagok számára rendelkezésre bocsátott megosztott postaládába, naptár, fájlok, SharePoint-webhelyre és sok egyéb révén. Ez a beállítás lehetővé teszi a szervezetnél kívüli személyek hozzáadását is a csoporthoz.

    - **Csoport neve.** Olyan nevet adjon a csoportnak, aminek értelme van és nem felejti el.

    - **Csoport leírása (nem kötelező).** Ha szeretne kiegészítő leírást adni a csoporthoz.

    - **Csoportházirend.** Válassza ki a lehetőséget, hogy mindenki csatlakozzon a csoporthoz, vagy csak a csoport tulajdonosa számára engedélyezze a tagok hozzáadását.

3. Kattintson a **Létrehozás** gombra.

    Az új csoport tulajdonosként jön létre, és megjelenik a **saját csoportok** listájában. Mivel Ön a tulajdonosa, ez a csoport is megjelenik a listán szereplő **csoportokban** .

## <a name="edit-an-existing-group"></a>Meglévő csoport szerkesztése

A csoport létrehozása után szerkesztheti annak részleteit, beleértve a meglévő információk frissítését is.

1. Jelölje ki a **csoportok** lapon a szerkeszteni kívánt csoportot, majd válassza a **részletek szerkesztése** lehetőséget a *&lt;group_name&gt;* oldalon.

    Megjelenik a **részletek szerkesztése** mező, és frissítheti a csoport első létrehozásakor hozzáadott adatokat.

2. Végezze el az összes módosítást, majd válassza a **frissítés**lehetőséget.

## <a name="add-or-remove-a-member"></a>Tag hozzáadása vagy eltávolítása

Hozzáadhat vagy eltávolíthat tagokat bármelyik saját csoport számára.

1. Válassza ki azt a csoportot, amelyhez hozzá kívánja adni a tagokat, majd válassza a **+** elemet a *&lt;group_name&gt;* oldalon.

    ![Csoporttag hozzáadása, a + jel kiemelve](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Keresse meg a hozzáadni kívánt tagot a **Tagok hozzáadása** mezőben, majd válassza a **Hozzáadás**lehetőséget.

    ![Tagok hozzáadása mező új tag hozzáadásával](media/my-apps-portal/my-apps-portal-add-member-page.png)

    A rendszer meghívót küld az új tagnak a szervezet alkalmazásai elérésének megkezdéséhez.

3. Ha tévedésből adta hozzá a tagot, vagy ha egy tag elhagyta a szervezetet, a **tag eltávolításához válassza a** tag neve melletti *&lt;group_name&gt;* lapot.

    ![Tag eltávolítása az eltávolítási hivatkozás kiemelésével](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Office 365-csoport megújítása

Ha a szervezete engedélyezi, megújíthat egy Office 365-csoportot, kiterjesztheti a lejárati dátumot.

1. Válassza ki a megújítani kívánt Office 365-csoportot, majd kattintson a **csoport megújítása**elemre.

    ![Office 365-csoport megújítása, a lejárati dátum meghosszabbítása](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. A megerősítő üzenet bezárásához kattintson **az OK** gombra.

    Az oldal frissítése után megtekintheti a **legutóbbi megújított** és a **csoport lejárati** dátumát.

## <a name="delete-a-group"></a>Csoport törlése

Bármikor törölheti a saját csoportjait. Ha azonban véletlenül töröl egy csoportot, létre kell hoznia, majd újra hozzá kell adnia a tagokat.

1. Válassza ki a véglegesen törölni kívánt csoportot, majd válassza a **csoport törlése** lehetőséget a *&lt;group_name&gt;* oldalon.

    ![< Group_name > oldalon a csoport törlése hivatkozás kiemelve](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Válassza az **Igen** lehetőséget a megerősítő üzenetben.

    A csoport véglegesen törölve lett.

## <a name="join-an-existing-group"></a>Csatlakozás meglévő csoporthoz

Egy már meglévő csoportot is csatlakozhat a **csoportok** lapról, vagy meghagyhatja azt.

1. A **csoportok** lapon válassza a **Csatlakozás csoportot** a csoportban lévő **csoportok** területen.

    Megjelenik az **illesztési csoportok** lap.

    ![Csatlakozás a csoportok laphoz, a csatlakozás csoport gombbal kiemelve](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Az **illesztési csoportok** lapon válassza ki annak a csoportnak a nevét, amelyhez csatlakozni szeretne, tekintse meg a társított csoport részleteit, majd ha a csoport elérhető, válassza a **Csatlakozás csoportot**.

    Ha a csoporthoz a csoport tulajdonosának jóvá kell hagynia a tagságot, a rendszer megkéri, hogy adjon meg egy üzleti indoklást, hogy miért van szükség a csoporthoz való csatlakozásra, majd válassza a **kérelem**lehetőséget. Ha a csoport nem igényel jóváhagyást, azonnal tagként lesz hozzáadva, és a csoport megjelenik a listán szereplő **csoportokban** .

3. Ha tévedésből csatlakozott egy csoporthoz, vagy ha már nincs szüksége rá, akkor kiválaszthatja a csoport nevét az **illesztési csoportok** lapon, majd válassza a **csoport elhagyása**lehetőséget.

    ![Csatlakozás a csoportok oldalhoz, a kilépés gomb kiemelésével](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>Következő lépések

- [Alkalmazások elérése és használata a saját alkalmazások portálon](my-apps-portal-end-user-access.md).

- [Módosítsa a profil adatait](my-apps-portal-end-user-update-profile.md).

- [Saját hozzáférési felülvizsgálatok végrehajtása](my-apps-portal-end-user-access-reviews.md).
