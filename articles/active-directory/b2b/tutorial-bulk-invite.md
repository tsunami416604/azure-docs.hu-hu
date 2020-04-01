---
title: Oktatóanyag a B2B együttműködési felhasználók tömeges meghívásához – Azure AD
description: Ebben az oktatóanyagban megismerheti, hogyan küldhet az Azure AD B2B együttműködés külső felhasználói számára tömeges meghívókat a PowerShell és egy CSV-fájl használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 2/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c429648adeb0c81799bff2dca1650de965395a60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77166439"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>Oktatóanyag: Tömeges meghívás az Azure AD B2B együttműködési felhasználóinak meghívása (előzetes verzió)

|     |
| --- |
| Ez a cikk az Azure Active Directory nyilvános előzetes verziójú szolgáltatását ismerteti. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!NOTE]
> 2019.02.12-től a Tömeges meghívás felhasználók (Előzetes verzió) funkció ideiglenesen le van tiltva.
> Jelenleg nincs ismert dátum, amikor ez a funkció újra engedélyezni fogja az Azure Portalon. A Vendégfelhasználók tömeges meghívásához a PowerShell használatával tekintse meg a [B2B tömeges meghívási oktatóanyagát](bulk-invite-powershell.md) vagy a [B2B-kódot és a PowerShell-mintákat.](code-samples.md)

Ha Azure Active Directory (Azure AD) B2B együttműködéssel dolgozik együtt külső partnerekkel, egyszerre több vendégfelhasználót meghívhat a szervezetébe. Ebben az oktatóanyagban megtudhatja, hogyan használhatja az Azure Portalon tömeges meghívókat külső felhasználóknak. A következőket fogja elvégezni:

> [!div class="checklist"]
> * **A Felhasználók tömeges meghívása (előzetes verzió)** segítségével vesszővel tagolt értékfájl (.csv) készüljön elő a felhasználói adatokkal és a meghívási beállításokkal
> * A .csv fájl feltöltése az Azure AD-be
> * A felhasználók címtárhoz való hozzáadásának ellenőrzése

Ha nem rendelkezik az Azure Active Directory, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz kettő vagy több e-mail-tesztfiókra, ahova a meghívókat küldheti. A fiókoknak a szervezeten kívüli kell lenniük. Bármilyen típusú fiókot használhat, így közösségi fiókokat is, például gmail.com vagy outlook.com végződésűeket.

## <a name="invite-guest-users-in-bulk"></a>Vendégfelhasználók meghívása ömlesztve

1. Jelentkezzen be az Azure Portalra egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
2. A navigációs ablakban válassza az **Azure Active Directory**lehetőséget.
3. A **Kezelés csoportban**válassza a **Felhasználók** > **tömeges meghívása lehetőséget.**
4. A **Tömeges meghívás felhasználók (előzetes verzió)** lapon válassza a **Letöltés** lehetőséget, ha érvényes .csv fájlt szeretne beszerezni a meghívás imasszal.

    ![Tömeges meghívás letöltése gomb](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Nyissa meg a .csv fájlt, és adjon hozzá egy sort minden vendégfelhasználóhoz. A szükséges értékek a következők:

   * **E-mail cím meghívni** - a felhasználó, aki kap egy meghívást

   * **Átirányítás url** - az URL-t, amelyre a meghívott felhasználó továbbítja a meghívás elfogadása után a meghívást

    ![Példa csv-fájlra, amelyen vendégfelhasználók léptek be](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Ne használjon vesszőt a **Testreszabott meghívóüzenetben,** mert azok megakadályozzák az üzenet sikeres elemzését.

6. Mentse a fájlt.
7. A **Felhasználók tömeges meghívása (előnézet)** lap **A csv-fájl feltöltése**területén keresse meg a fájlt. A fájl kijelölésétorként megkezdődik a .csv fájl érvényesítése. 
8. A fájl tartalmának ellenőrzése után a **fájl feltöltése sikeresen megjelenik.** Ha vannak hibák, ki kell javítania azokat, mielőtt elküldheti a feladatot.
9. Amikor a fájl megfelel az ellenőrzésen, válassza **a Küldés** lehetőséget a meghívókat hozzáadó Azure tömeges művelet elindításához. 
10. A feladat állapotának megtekintéséhez válassza a Kattintson ide lehetőséget **az egyes műveletek állapotának megtekintéséhez.** Vagy kiválaszthatja **a Tömeges művelet eredményeit (előzetes verzió)** a **Tevékenység** szakaszban. A tömeges művelet egyes sorainak részleteiért válassza ki a **# Sikeres**, **# Sikertelen**vagy Összes **kérelem** oszlop ban található értékeket. Ha hiba történt, a hiba okai jelennek meg a listában.

    ![Példa a tömeges műveletek eredményeire](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Amikor a feladat befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeres volt.

## <a name="verify-guest-users-in-the-directory"></a>A címtárvendég-felhasználók ellenőrzése

Ellenőrizze, hogy a hozzáadott vendégfelhasználók léteznek-e a címtárban az Azure Portalon vagy a PowerShell használatával.

### <a name="view-guest-users-in-the-azure-portal"></a>Vendégfelhasználók megtekintése az Azure Portalon

1. Jelentkezzen be az Azure Portalra egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
2. A navigációs ablakban válassza az **Azure Active Directory**lehetőséget.
3. A **Kezelés** alatt válassza a **Felhasználókat**.
4. A **Megjelenítés**csoportban válassza a **Csak Vendégfelhasználók** lehetőséget, és ellenőrizze, hogy a hozzáadott felhasználók szerepelnek-e a listában.

### <a name="view-guest-users-with-powershell"></a>Vendégfelhasználók megtekintése a PowerShell használatával

Futtassa az alábbi parancsot:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Meg kell jelennie a meghívott felhasználóknak a listában, egy egyszerű felhasználónévvel (UPN) az *e-mail cím*#EXT#\@*tartomány*formátumában. Például *lstokes_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, ahol contoso.onmicrosoft.com az a szervezet, amelytől a meghívókat küldte.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti a teszt felhasználói fiókokat a címtárban az Azure Portalon a Felhasználók lapon, ha bejelöli a felhasználó melletti jelölőnégyzetet, majd a **Törlés**lehetőséget. 

Felhasználói fiók törléséhez a következő PowerShell-paranccsal is futtathatja a következő ta-

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Például:`Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban tömeges meghívókat küldött szervezeten kívüli vendégfelhasználóknak. A következő szakaszban megtudhatja, hogyan működik a meghívások érvényesítési folyamata.

> [!div class="nextstepaction"]
> [További információ az Azure AD B2B együttműködés meghívóérvényesítési folyamatáról](redemption-experience.md)
