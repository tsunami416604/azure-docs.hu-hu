---
title: A csoportok adatait, a saját alkalmazások portál – Azure Active Directory frissítése |} A Microsoft Docs
description: Ismerje meg, hogyan megtekintése és módosítása a csoportok kapcsolatos információkat, beleértve a saját csoportok megtekintése, új csoportok létrehozása, megtekintése, amelyhez már tagja, és minden összekötő azokat, a csoportok már nem része.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5af86ad12eed4901e577149b9f20216d5004663b
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340461"
---
# <a name="update-your-groups-info-from-the-my-apps-portal"></a>A csoportok adatait, a saját alkalmazások portál frissítése
A munkahelyi vagy iskolai fiók használata a web-alapú **saját alkalmazások** portálon megtekintheti, és indítsa el a számos szervezet felhőalapú alkalmazásokat, a profil és fiók információk megjelenítéséhez frissíteni, a **csoportok** információkat, és végrehajtásához **hozzáférési felülvizsgálatokkal** az alkalmazások és a csoportokat. Ha nem rendelkezik hozzáféréssel a **saját alkalmazások** portál, forduljon a Segélyszolgálathoz engedélyt.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Ha Ön rendszergazda, és a felhőalapú alkalmazások kezelése a további tájékoztatást talál a [Alkalmazásfelügyelet dokumentációja](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="view-your-groups-information"></a>A csoportok adatainak megtekintése
Ha a rendszergazda engedélyt adott megtekintéséhez a **csoportok** csempén is:

- **Csoport tagjaként.** A részletek megtekintéséhez, vagy hagyja meg az egyetlen csoportnak sem.

- **A csoport tulajdonosa.** A részletek megtekintéséhez, hozzon létre egy új csoportot, adjon hozzá tagok eltávolítása, vagy törölje a csoportot.

### <a name="to-view-your-groups-information"></a>A csoportok információk megtekintése

1.  Jelentkezzen be munkahelyi vagy iskolai fiókjával.

2.  Nyissa meg a webböngészőjét, és nyissa meg https://myapps.microsoft.com, vagy használja a szervezet által biztosított hivatkozást. Például akkor lehet, hogy átirányítja egy testre szabott lap a szervezete számára például https://myapps.microsoft.com/contoso.com.

    A **alkalmazások** lap jelenik meg, a felhőalapú alkalmazások megjelenítése, a szervezet tulajdonában és elérhető a használatra.

    ![Saját alkalmazások portál alkalmazások lapján](media/my-apps-portal/my-apps-portal-apps-page-groups-tile.png)

3. Válassza ki a **csoportok** csempére a csoporttal kapcsolatos információk megtekintéséhez.

    ![Birtokolt tartalmazó csoportok lapon és a tag csoportok](media/my-apps-portal/my-apps-portal-groups-page.png)

4. Az engedélyek alapján, használhatja a **csoportok** lap:

    - **Tekintse át a saját csoportok.** A szervezeten belüli saját nézet csoportot tájékozódhat a **saját csoportok** területen. Egy adott csoport nevének kijelölésekor nyújt további információt a csoport, beleértve a csoport típusának, tagok száma, az illesztési házirendet és az aktív tagok listája.

    - **Hozzon létre egy új csoportot.** Új csoport létrehozása az Ön a tulajdonosa az a **saját csoportok** területen. Adott lépéseiért lásd: [hozzon létre egy új csoportot](#create-a-new-group) című szakaszát.

    - **Módosítsa egy meglévő csoportot.** A saját csoportok adatainak szerkesztése. Adott lépéseiért lásd: [szerkesztheti a meglévő csoport](#edit-an-existing-group) című szakaszát.

    - **Tagok hozzáadása vagy eltávolítása.** Tagok hozzáadása vagy eltávolítása a saját csoportok. Adott lépéseiért lásd: [hozzáadása vagy eltávolítása tag](#add-or-remove-a-member) című szakaszát.

    - **Csoport törlése.** Saját csoportok törlése. Adott lépéseiért lásd: [csoport törléséhez](#delete-a-group) című szakaszát.

    - **Tekintse át a csoportokat egy részét.** A neveket, minden olyan csoportok, amelyhez Ön a tagja a **csoporttagságok** területen. Egy adott csoport nevének kijelölésekor nyújt további információt a csoport, beleértve a csoport típusának, tagok száma, az illesztési házirendet és az aktív tagok listája.

    - **Csatlakozás a csoporthoz.** Csatlakozás meglévő csoporthoz, amelyhez még nem tagja, az a **csoporttagságok** területen. Adott lépéseiért lásd: [csatlakozás meglévő csoporthoz](#join-an-existing-group).

## <a name="create-a-new-group"></a>Új csoport létrehozása
1. Az a **csoportok** lapon jelölje be **hozzon létre egy csoportot** származó a **saját csoportok** területen.

    A **csoport létrehozása** mező jelenik meg.

    ![Csoport létrehozása](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Adja meg a szükséges adatokat:

    - **Csoport típusa:**
        
        - **Biztonság.** Segítségével kezelheti a tag és a számítógép hozzáféréseket a megosztott erőforrásokhoz a felhasználók egy csoportjára. Például létrehozhat egy biztonsági csoportot egy meghatározott biztonsági házirendhez. Ha így tesz, az összes tagnak egyszerre oszthatja ki az engedélyeket, nem kell az engedélyeket minden tagnak külön kiadni.

        - **Office 365.** Együttműködési lehetőségeket biztosít a tagok számára rendelkezésre bocsátott megosztott postaládába, naptár, fájlok, SharePoint-webhelyre és sok egyéb révén. Ez a beállítás lehetővé teszi a szervezetnél kívüli személyek hozzáadását is a csoporthoz.

    - **Csoport neve.** Olyan nevet adjon a csoportnak, aminek értelme van és nem felejti el.

    - **A csoport leírása (nem kötelező).** Ha szeretne kiegészítő leírást adni a csoporthoz.

    - **Csoport házirend.** Válasszon, hogy mindenki a csoporthoz való csatlakozásra, illetve a kizárólag a tagokat a csoport tulajdonosa.

3. Kattintson a **Létrehozás** gombra.

    Az új csoport jön létre az Ön a tulajdonosa, és megjelenik a **saját csoportok** listája. Mivel Ön a tulajdonosa, ez a csoport is megjelenik a **csoporttagságok** listája.

## <a name="edit-an-existing-group"></a>Meglévő csoport szerkesztése
Miután létrehozott egy csoportot, szerkesztheti a részleteket, például a meglévő adatok frissítése.

### <a name="to-edit-your-details"></a>A Részletek szerkesztése
1. Válassza ki a szerkeszteni kívánt csoportot a **csoportok** oldalra, és kattintson **részleteinek szerkesztése** a a *&lt;csoportnév&gt;* lapot.

    A **részleteinek szerkesztése** mező jelenik meg, és frissítheti az adatokat, amikor először hozza létre a csoporthoz hozzáadott.

2. Győződjön meg az összes módosítást, és válassza ki **frissítés**.

## <a name="add-or-remove-a-member"></a>Adja hozzá, vagy a tag eltávolítása
Adja hozzá, vagy a tagok eltávolítása a saját csoportok bármelyikéhez.

### <a name="to-add-or-remove-a-member"></a>Hozzáadni vagy eltávolítani egy tag
1. Válassza ki a csoportot, hogy tagokat adjanak hozzá, és válassza ki **+** a a *&lt;csoportnév&gt;* lapot.

    ![Adjon hozzá egy csoport tagjaként + bejelentkezési kiemelésével](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Keresse meg a tag szeretne hozzáadni, az a **tagok hozzáadása** mezőbe, majd válassza ki **Hozzáadás**.

    ![Adja hozzá a tagokat mezőbe az új tag hozzáadása](media/my-apps-portal/my-apps-portal-add-member-page.png)

    Meghívót küldött, az új tag a kezdéshez fér hozzá a munkahelyi alkalmazásokhoz.

3. Ha véletlenül a tag hozzáadva, vagy ha tagja a szervezet rendelkezik marad, a tag kiválasztásával eltávolíthatja **tag eltávolítása** a a tag neve melletti a *&lt;csoportnév&gt;* lapot.

    ![Távolítsa el a tagja, az Eltávolítás hivatkozás kiemelésével](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="delete-a-group"></a>Csoport törlése
Saját csoportok bármelyike bármikor törölheti. Azonban ha véletlenül törli a csoportot fogja akkor hozza létre és felhasználókat adhatnának újra.

### <a name="to-delete-the-group"></a>A csoport törlése
1. Válassza ki a csoport végleges törlése, és válassza ki a kívánt **csoport törlése** a a *&lt;csoportnév&gt;* lapot.

    ![< Csoportnév > lapja, a törlés csoport hivatkozás kiemelésével](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Válassza ki **Igen** a megerősítő üzeneten.

    A csoport az véglegesen törlődni fog. 
    
## <a name="join-an-existing-group"></a>Csatlakozás meglévő csoporthoz
A már létező csoport is csatlakozhat a **csoportok** lapot.

### <a name="to-join-or-leave-a-group"></a>Való csatlakozásra és a egy csoportot

1. Az a **csoportok** lapon jelölje be **csatlakozás a csoporthoz** származó a **csoporttagságok** területen.

    A **csatlakozás csoportokhoz** lap jelenik meg.

    ![Csoportok lapon Közösséghez való csatlakozás csoport gomb](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Az a **csatlakozás csoportokhoz** lapon, válassza ki a kívánt csatlakozzon, a társított csoport részleteinek megtekintéséhez, és ha érhető el a csoportot, válassza a csoport nevét **csatlakozás a csoporthoz**.

    Ha a csoport tagsága jóváhagyása a csoport tulajdonosa van szüksége, miért van szükség a csoporthoz való csatlakoztatáshoz üzleti indoklásának meg kell adnia, és válassza **kérelem**. Ha a csoport nincs szüksége jóváhagyásra, akkor azonnal hozzáadják, amelynek, és megjelenik a csoport a **csoporttagságok** listája.

3. Ha véletlenül csatlakoztatott egy csoportot, vagy ha már nincs szüksége, azt egy részét, a csoport nevét, válassza a **csatlakozás csoportokhoz** oldalra, és kattintson **Kilépés csoportból**.

    ![Csatlakozzon a csoportok lapon hagyja csoport gomb kiemelésével](media/my-apps-portal/my-apps-portal-leave-group-link.png)    

## <a name="next-steps"></a>További lépések

- [Elérheti és használni az alkalmazásokat a saját alkalmazások portál](my-apps-portal-end-user-access.md).

- [Módosítsa a profiladatok](my-apps-portal-end-user-update-profile.md).

- [Hajtsa végre a saját hozzáférési felülvizsgálatok](my-apps-portal-end-user-access-reviews.md).