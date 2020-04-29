---
title: Oktatóanyag a B2B együttműködési felhasználók tömeges meghívásához – Azure AD
description: Ebben az oktatóanyagban megismerheti, hogyan küldhet az Azure AD B2B együttműködés külső felhasználói számára tömeges meghívókat a PowerShell és egy CSV-fájl használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/13/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ef9172ca5d0961bb6de1949a61199ce1d6c1bff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81603421"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Oktatóanyag: Azure AD B2B együttműködési felhasználók tömeges meghívása

Ha Azure Active Directory (Azure AD) B2B együttműködéssel dolgozik együtt külső partnerekkel, egyszerre több vendégfelhasználót meghívhat a szervezetébe. Ebből az oktatóanyagból megtudhatja, hogyan küldhet tömeges meghívásokat a külső felhasználóknak a Azure Portal használatával. A következőket fogja elvégezni:

> [!div class="checklist"]
> * A **felhasználók tömeges meghívásával** készítse el a vesszővel tagolt (. csv) fájlt a felhasználói adatok és a Meghívási beállítások használatával.
> * Töltse fel a. csv fájlt az Azure AD-be
> * A felhasználók címtárhoz való hozzáadásának ellenőrzése

Ha nincs Azure Active Directory, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz kettő vagy több e-mail-tesztfiókra, ahova a meghívókat küldheti. A fiókoknak a szervezeten kívüli kell lenniük. Bármilyen típusú fiókot használhat, így közösségi fiókokat is, például gmail.com vagy outlook.com végződésűeket.

## <a name="invite-guest-users-in-bulk"></a>Vendég felhasználóinak meghívása ömlesztve

1. Jelentkezzen be a Azure Portalba egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
2. A navigációs ablaktáblán válassza a **Azure Active Directory**lehetőséget.
3. A **kezelés**területen válassza a **felhasználók** > **tömeges meghívás**lehetőséget.
4. A **felhasználók tömeges meghívása** lapon válassza a **Letöltés** lehetőséget a meghívó tulajdonságokkal rendelkező érvényes. csv fájl beszerzéséhez.

    ![Tömeges meghívás letöltése gomb](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Nyissa meg a. csv-fájlt, és adjon hozzá egy sort minden vendég felhasználóhoz. A szükséges értékek a következők:

   * **Meghívó e-mail-cím** – a meghívót kérő felhasználó

   * **Átirányítási URL** -cím – az az URL-cím, amelyre a meghívott felhasználó továbbítva lett a meghívó elfogadása után

    ![Példa a vendég felhasználókat tartalmazó CSV-fájlra](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Ne használjon vesszőket a **testreszabott Meghívási üzenetben** , mert megakadályozza, hogy az üzenet elemzése sikeres legyen.

6. Mentse a fájlt.
7. A **felhasználók tömeges meghívása** lapon a **CSV-fájl feltöltése**területen keresse meg a fájlt. A fájl kiválasztásakor elindul a. csv-fájl érvényesítése. 
8. A fájl tartalmának ellenőrzésekor a **fájl feltöltése sikeresen**megtörténik. Ha hibák léptek fel, ezeket a feladatok elküldése előtt ki kell javítania.
9. Ha a fájl érvényesíti az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget a meghívókat felvenni kívánó Azure tömeges művelet elindításához. 
10. A feladatok állapotának megtekintéséhez válassza a **kattintson ide az egyes műveletek állapotának megtekintéséhez**. Vagy kiválaszthatja a **tömeges művelet eredményeit** is a **tevékenység** szakaszban. A tömeges művelet minden egyes sorával kapcsolatos részletekért jelölje ki az értékeket a **# sikeres**, **# sikertelen**vagy az **összes kérelem** oszlopban. Ha hiba történt, a hiba okai lesznek felsorolva.

    ![Példa tömeges művelet eredményeire](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Amikor a feladatok befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeresen befejeződött.

## <a name="verify-guest-users-in-the-directory"></a>Vendég felhasználók ellenőrzése a címtárban

Ellenőrizze, hogy a hozzáadott vendég felhasználók szerepelnek-e a könyvtárban a Azure Portal vagy a PowerShell használatával.

### <a name="view-guest-users-in-the-azure-portal"></a>Vendég felhasználók megtekintése a Azure Portalban

1. Jelentkezzen be a Azure Portalba egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
2. A navigációs ablaktáblán válassza a **Azure Active Directory**lehetőséget.
3. A **Kezelés** alatt válassza a **Felhasználókat**.
4. A **Megjelenítés**területen válassza ki a **csak vendég felhasználók** elemet, és ellenőrizze, hogy a hozzáadott felhasználók szerepelnek-e a listáján.

### <a name="view-guest-users-with-powershell"></a>Vendég felhasználók megtekintése a PowerShell-lel

Futtassa az alábbi parancsot:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

A meghívott felhasználókat a következő formátumban kell megjelennie: *EmailAddress*#EXT #\@*tartomány*. Például *lstokes_fabrikam. com # ext #\@contoso.onmicrosoft.com*, ahol a contoso.onmicrosoft.com az a szervezet, amelyről elküldötte a meghívókat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti a felhasználói fiókokat a Azure Portal a felhasználók lapon, ha bejelöli a vendég felhasználó melletti jelölőnégyzetet, majd kiválasztja a **Törlés**lehetőséget. 

Vagy futtathatja a következő PowerShell-parancsot egy felhasználói fiók törléséhez:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Például:`Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban tömeges meghívókat küldött szervezeten kívüli vendégfelhasználóknak. A következő szakaszban megtudhatja, hogyan működik a meghívások érvényesítési folyamata.

> [!div class="nextstepaction"]
> [További információ az Azure AD B2B együttműködés meghívóérvényesítési folyamatáról](redemption-experience.md)
