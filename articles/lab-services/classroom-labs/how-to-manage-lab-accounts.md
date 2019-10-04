---
title: Az Azure Lab Services tesztkörnyezetfiókok kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan tesztkörnyezetfiók létrehozása, megtekintheti az összes tesztkörnyezetfiókok vagy törölheti a tesztkörnyezet Azure-előfizetéssel.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 6f283ce007e96547e01a01a3753ddcb60574bfc3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65412807"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Az Azure Lab Services tesztkörnyezetfiókok kezelése 
Az Azure Lab Services tesztkörnyezetfiók az felügyelt tesztkörnyezet-típusok, például osztályterem-tesztkörnyezetek tárolója. A rendszergazda állítja be az Azure Lab Services tesztkörnyezetfiók és labortulajdonosok labs hozhat létre a fiók számára hozzáférést biztosít. Ez a cikk bemutatja, hogyan tesztkörnyezetfiók létrehozása, megtekintheti az összes tesztkörnyezetfiókok, vagy törölheti a labor.

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
A következő lépések bemutatják, hogyan használhatja az Azure Portalt tesztkörnyezetfiók létrehozására az Azure Lab Services szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** a bal oldali menüben. Válassza ki **Tesztkörnyezetfiókok** a a **fejlesztési és üzemeltetési** szakaszban. Ha bejelöli a csillag (`*`) melletti **Tesztkörnyezetfiókok**, megjelenik a **Kedvencek** szakaszban a bal oldali menüben. És újabb verziók esetében a következő időpont választja **Tesztkörnyezetfiókok** alatt **Kedvencek**.

    ![Az összes szolgáltatás -> Lab-fiókokban](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Az a **Tesztkörnyezetfiókok** lapon jelölje be **Hozzáadás** az eszköztáron. 

    ![A Lab-fiókokban oldalon válassza a Hozzáadás](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. Az a **labor fiók** lapon, tegye a következőket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget, majd adjon nevet az új erőforráscsoportnak.
    4. A **Helyszín** esetében válassza ki a helyszínt/régiót, ahol létre szeretné hozni a tesztkörnyezetfiókot. 
    5. Válasszon egy meglévő **megosztott lemezkép-katalógusában** vagy hozzon létre egyet. A Virtuálisgép-sablon mentheti a megosztott rendszerkép-katalógusában ahhoz, hogy a mások által felhasználható. Megosztott kép katalógusok részletes információkért lásd: [használata az Azure Lab Services egy megosztott képgyűjtemény](how-to-use-shared-image-gallery.md).
    6. A **társ virtuális hálózatnak**, válassza ki a társ virtuális hálózatnak (VNet), a tesztlabor-hálózat. Ehhez a fiókhoz a létrehozott Labs a kiválasztott virtuális hálózat csatlakozik, és rendelkezik az erőforrásokhoz való hozzáférést a kiválasztott virtuális hálózat. 
    7. Adjon meg egy **címtartomány** a laborkörnyezetben található virtuális gépek számára. A címtartomány a classless inter-domain routing (CIDR) formátumban kell lennie (Példa: 10.20.0.0/23). A lab-ben a virtuális gépek jönnek a címtartományt. További információkért lásd: [-címtartományt ad meg a virtuális gépek a laborban](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab).    
    8. Az a **engedélyezése tesztkörnyezet létrehozója, labor helyre** mezőben adja meg, hogy tesztlabor kiindulópontként szolgálhat ki egy helyet a tesztkörnyezethez. Alapértelmezés szerint a beállítás le van tiltva. Le van tiltva, ha a labor alkotói nem adható meg egy helyet a tesztkörnyezethez hoznak létre. A labs labor-fiókba az Önhöz legközelebbi földrajzi helyet jönnek létre. Ha engedélyezve van, egy tesztkörnyezet létrehozója lab létrehozása idején válasszon egy helyet is.      
    9. Kattintson a **Létrehozás** gombra. 

        ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Válassza ki a **harang ikonra** eszköztár (**értesítések**), győződjön meg arról, hogy az üzembe helyezés sikeres volt-e, és válassza ki **erőforrás megnyitása**. 

    Jelölje ki **frissítése** a a **Tesztkörnyezetfiókok** lapon, és válassza ki a létrehozott tesztkörnyezet-fiókot. 

    ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Megjelenik az alábbi **tesztkörnyezetfiók** oldala:

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>Tesztkörnyezetfiókok megtekintése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **összes erőforrás** a menüből. 
3. Válassza ki **Tesztkörnyezetfiókok** számára a **típus**. 
    Előfizetés, erőforráscsoport, helyek és címkék szerint is szűrheti. 

    ![Az összes erőforrás -> Lab-fiókokban](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>Megtekintheti és kezelheti a labs labor-fiókban

1. Az a **labor fiók** lapon jelölje be **Labs** a bal oldali menüben.

    ![A fiókban lévő Labs](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. Megjelenik egy **tesztkörnyezetek listájában** a számlán a következő információkat: 
    1. A labor nevét.
    2. A dátum, amelyen a labor létrehozása. 
    3. E-mail-címét a felhasználó, aki létrehozta a labor létrehozása. 
    4. A laborkörnyezetbe engedélyezett felhasználók maximális száma. 
    5. A labor állapotát. 

## <a name="delete-a-lab-in-the-lab-account"></a>A lab-fiókban tesztkörnyezet törlése
Kövesse az utasításokat az előző szakaszban a labor-fiókban a labs listájának megtekintéséhez.

1. Válassza ki **... három (pont)** , és válassza ki **törlése**. 

    ![Tesztkörnyezet törlése – gomb](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. Válassza ki **Igen** a figyelmeztető üzenet jelenik meg. 

    ![Labor törlésének megerősítése](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>Tesztkörnyezetfiók törlése
Kövesse az utasításokat az előző szakaszban, hogy a lab-fiókokban listában jeleníti meg. Tesztkörnyezetfiók törléséhez kövesse az alábbi utasításokat: 

1. Válassza ki a **labor fiók** , amelyet törölni szeretne. 
2. Válassza ki **törlése** az eszköztáron. 

    ![Tesztkörnyezetfiókok -> a Törlés gombra.](../media/how-to-manage-lab-accounts/delete-button.png)
1. Típus **Igen** megerősítést.
1. Válassza a **Törlés** elemet. 

    ![Törölje a labor-fiók – megerősítés](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)


## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikket: [Tesztkörnyezetfiókok konfigurálása](how-to-configure-lab-accounts.md).