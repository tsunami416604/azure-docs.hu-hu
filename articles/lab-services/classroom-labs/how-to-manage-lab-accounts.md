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
ms.date: 02/07/2018
ms.author: spelluru
ms.openlocfilehash: 6cd06778ad54fa698c5bc2fe4ccf02f4be2ee2ec
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807046"
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
    5. Kattintson a **Létrehozás** gombra. 

        ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Válassza ki a **harang ikonra** eszköztár (**értesítések**), győződjön meg arról, hogy az üzembe helyezés sikeres volt-e, és válassza ki **erőforrás megnyitása**. 

    Jelölje ki **frissítése** a a **Tesztkörnyezetfiókok** lapon, és válassza ki a létrehozott tesztkörnyezet-fiókot. 

    ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. Megjelenik az alábbi **tesztkörnyezetfiók** oldala:

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha ugyanazt a felhasználói fiókot tervezi használni az osztályterem-tesztkörnyezet létrehozásához, ezt a lépést kihagyhatja. Ha egy másik felhasználói fiókot tervez használni, kövesse a következő lépéseket: 

Ha engedélyt kíván adni az oktatóknak, hogy létrehozzák a tesztkörnyezeteket a tanóráikhoz, adja hozzá őket a **Tesztkörnyezet-létrehozó** szerepkörhöz:

1. A a **labor fiók** lapon válassza ki **hozzáférés-vezérlés (IAM)**, és kattintson a **+ szerepkör-hozzárendelés hozzáadása** az eszköztáron. 

    ![Hozzáférés-vezérlés -> szerepkör-hozzárendelés hozzáadása gomb](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Az a **szerepkör-hozzárendelés hozzáadása** lapon jelölje be **tesztkörnyezet létrehozója** a **szerepkör**, válassza ki a felhasználót, adja hozzá a labor létrehozó szerepkörhöz, és válassza ki a kívánt **mentése**. 

    ![Tesztkörnyezet létrehozója hozzáadása](../media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="specify-marketplace-images-available-to-lab-creators"></a>Adja meg a Marketplace-rendszerképek labor létrehozói számára elérhető
A tesztkörnyezetfiók tulajdonosaként megadhatja azokat a Marketplace-beli rendszerképeket, amelyek használatával a tesztkörnyezet-létrehozók tesztkörnyezeteket hozhatnak létre a tesztkörnyezetfiókban. 

1. Válassza a bal oldali menüből a **Marketplace-beli rendszerképek** elemet. Alapértelmezés szerint a rendszerképek teljes listája jelenik meg (az engedélyezett és a letiltott rendszerképek egyaránt). A fenti legördülő listában a **Csak az engedélyezettek**/**Csak a letiltottak** lehetőségre kattintva szűrheti a listát, hogy csak az engedélyezett/letiltott rendszerképek jelenjenek meg. 
    
    ![Marketplace-beli rendszerképek oldal](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    A listában csak azok a Marketplace-beli rendszerképek jelennek meg, amelyek megfelelnek a következő feltételeknek:
        
    - Egyetlen virtuális gépet hoznak létre.
    - A virtuális gépek üzembe helyezéséhez az Azure Resource Managert használják.
    - Nincs szükség külön licenccsomag vásárlására.
2. Egy engedélyezett Marketplace-beli rendszerkép **letiltásának** a következő módjai vannak: 
    1. Kattintson az utolsó oszlopban a három pontra **(...)**, majd a **Rendszerkép letiltása** lehetőségre. 

        ![Egy rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Jelöljön ki egy vagy több, a listában szereplő rendszerképet a nevük előtti jelölőnégyzet kijelölésével, majd kattintson a **Kiválasztott rendszerképek letiltása** lehetőségre. 

        ![Több rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. A letiltott Marketplace-beli rendszerképek **engedélyezésének** a fentiekhez hasonlóan a következő módjai vannak: 
    1. Kattintson az utolsó oszlopban a három pontra **(...)**, majd a **Rendszerkép engedélyezése** lehetőségre. 
    2. Jelöljön ki egy vagy több, a listában szereplő rendszerképet a nevük előtti jelölőnégyzet kijelölésével, majd kattintson a **Kiválasztott rendszerképek engedélyezése** lehetőségre. 

## <a name="view-lab-accounts"></a>Tesztkörnyezetfiókok megtekintése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **összes erőforrás** a menüből. 
3. Válassza ki **Tesztkörnyezetfiókok** számára a **típus**. 
    Előfizetés, erőforráscsoport, helyek és címkék szerint is szűrheti. 

    ![Az összes erőforrás -> Lab-fiókokban](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>Tesztkörnyezetfiók törlése
Kövesse az utasításokat az előző szakaszban, hogy a lab-fiókokban listában jeleníti meg. Tesztkörnyezetfiók törléséhez kövesse az alábbi utasításokat: 

1. Válassza ki a **labor fiók** , amelyet törölni szeretne. 
2. Válassza ki **törlése** az eszköztáron. 

    ![Tesztkörnyezetfiókok -> a Törlés gombra.](../media/how-to-manage-lab-accounts/delete-button.png)
1. Típus **Igen** megerősítést.
1. Válassza a **Törlés** elemet. 

    ![Törölje a labor-fiók – megerősítés](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

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

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Labortulajdonosként hozzon létre és laborok kezelése](how-to-manage-classroom-labs.md)
- [Labortulajdonosként állítsa be, és a sablonok közzététele](how-to-create-manage-template.md)
- [Labortulajdonosként konfigurálása, és a egy lab használatának szabályozása](how-to-configure-student-usage.md)
- [Labor felhasználóként osztályterem-tesztkörnyezetek elérése](how-to-use-classroom-lab.md)