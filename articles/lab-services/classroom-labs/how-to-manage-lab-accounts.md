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
ms.date: 11/15/2018
ms.author: spelluru
ms.openlocfilehash: c672634e79cbc0850edfe7f7fbdb0a880d5cf0d8
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51707129"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>Az Azure Lab Services tesztkörnyezetfiókok kezelése 
Az Azure Lab Services tesztkörnyezetfiók az osztályterem-tesztkörnyezetek például felügyelt tesztkörnyezetek tárolója. A rendszergazda állítja be az Azure Lab Services tesztkörnyezetfiók és labortulajdonosok labs hozhat létre a fiók számára hozzáférést biztosít. Ez a cikk bemutatja, hogyan tesztkörnyezetfiók létrehozása, megtekintheti az összes tesztkörnyezetfiókok, vagy törölheti a labor.

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali főmenüben válassza az **Erőforrás létrehozása** lehetőséget.
3. Keressen a **Lab Services** szövegre az Azure Marketplace-en, és válassza a **Lab Services** alkalmazást a legördülő listából. 
4. Válassza a **Lab Services (előzetes verzió)** lehetőséget a szűrt szolgáltatáslistában. 
5. A **Tesztkörnyezetfiók létrehozása** ablakban válassza a **Létrehozás** elemet.
7. A **Tesztkörnyezetfiók** ablakban tegye a következőket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget, majd adjon nevet az új erőforráscsoportnak.
    4. A **Helyszín** esetében válassza ki a helyszínt/régiót, ahol létre szeretné hozni a tesztkörnyezetfiókot. 
    5. Kattintson a **Létrehozás** gombra. 

        ![Tesztkörnyezetfiók létrehozása ablak](../media/how-to-manage-lab-accounts/lab-account-settings.png)
5. Ha a tesztkörnyezetfiók oldala nem látható, kattintson az **Értesítések** gombra, majd az **Ugrás ide: erőforrás** gombra az értesítésekben. 

    ![Tesztkörnyezetfiók létrehozása ablak](../media/how-to-manage-lab-accounts/notification-go-to-resource.png)    
6. Megjelenik az alábbi **tesztkörnyezetfiók** oldala:

    ![Tesztkörnyezetfiók-oldal](../media/how-to-manage-lab-accounts/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha ugyanazt a felhasználói fiókot tervezi használni az osztályterem-tesztkörnyezet létrehozásához, ezt a lépést kihagyhatja. Ha egy másik felhasználói fiókot tervez használni, kövesse a következő lépéseket: 

1. A **Tesztkörnyezetfiók** oldalon válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget, majd kattintson az eszköztár **+ Hozzáadás** elemére. 

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/access-control.png)
2. Az **Engedélyek hozzáadása** oldal **Szerepkör** területén válassza a **Tesztkörnyezet-létrehozó** lehetőséget, válassza ki a Tesztkörnyezet-létrehozó szerepkörhöz hozzáadni kívánt felhasználót, majd kattintson a **Mentés** gombra. 

    ![Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>A tesztkörnyezet-tulajdonosok számára elérhető rendszerképek megadása a Marketplace-en
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
3. Válassza ki **Lab Services** számára a **típus**. 
    Előfizetés, erőforráscsoport, helyek és címkék szerint is szűrheti. 

## <a name="delete-a-lab-account"></a>Tesztkörnyezetfiók törlése
Kövesse az utasításokat az előző szakaszban, hogy a lab-fiókokban listában jeleníti meg. Tesztkörnyezetfiók törléséhez kövesse az alábbi utasításokat: 

1. Válassza ki a **labor fiók** , amelyet törölni szeretne. 
2. Válassza ki **törlése** az eszköztáron. 
3. Típus **Igen** megerősítést.
4. Válassza a **Törlés** elemet. 

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



## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Labortulajdonosként hozzon létre és laborok kezelése](how-to-manage-classroom-labs.md)
- [Labortulajdonosként állítsa be, és a sablonok közzététele](how-to-create-manage-template.md)
- [Labortulajdonosként konfigurálása, és a egy lab használatának szabályozása](how-to-configure-student-usage.md)
- [Labor felhasználóként osztályterem-tesztkörnyezetek elérése](how-to-use-classroom-lab.md)