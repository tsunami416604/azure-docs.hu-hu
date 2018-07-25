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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: ff2968f8e2fa9a705817b020f2daa6582d78029c
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225302"
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
Osztályterem-tesztkörnyezet tesztkörnyezetfiók beállítása, hogy a felhasználó tagjának kell lennie a **tesztkörnyezet létrehozója** szerepkör a labor-fiókban. A lab-fiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha azt tervezi, ugyanazzal a fiókkal használandó osztályterem-tesztkörnyezet létrehozása, kihagyhatja ezt a lépést. Osztályterem-tesztkörnyezet létrehozni egy másik felhasználói fiók használatával, kövesse az alábbi lépéseket: 

1. A **Tesztkörnyezetfiók** oldalon válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget, majd kattintson az eszköztár **+ Hozzáadás** elemére. 

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/access-control.png)
2. Az **Engedélyek hozzáadása** oldal **Szerepkör** területén válassza a **Tesztkörnyezet-létrehozó** lehetőséget, válassza ki a Tesztkörnyezet-létrehozó szerepkörhöz hozzáadni kívánt felhasználót, majd kattintson a **Mentés** gombra. 

    ![Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>Adja meg a Marketplace-rendszerképek labortulajdonosok számára elérhető
Ebben a szakaszban adja meg, amely labortulajdonosok használatával hozhat létre az osztályterem-tesztkörnyezetek Marketplace-rendszerképek. 

1. Válassza ki **Piactérről származó rendszerképek** a a bal oldali menüben. Alapértelmezés szerint (egyaránt engedélyezve van, és le van tiltva) teljes listáját láthatja. A listában, csak az engedélyezett/letiltott-lemezképek kiválasztásával szűrheti a **csak engedélyezett**/**csak letiltja** lehetőséget a legördülő listából válassza ki a tetején. 

    ![Piactéri lemezképek lap](../media/tutorial-setup-lab-account/marketplace-images-page.png)
2. A **letiltása** Piactéri lemezképet, amely engedélyezve van, hajtsa végre az alábbi műveletek egyikét: 
    1. Válassza ki **... három (pont)**  az utolsó oszlopra, és válassza a **letiltása lemezkép**. 

        ![Egy kép letiltása](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Válasszon ki egy vagy több lemezképet a listából válassza ki a jelölőnégyzetek a rendszerkép neve előtt a listában, és válassza ki **tiltsa le a megadott lemezképek**. 

        ![Több lemezképet letiltása](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Ehhez hasonlóan a **engedélyezése** a Piactéri rendszerkép, tegye a következő műveletek egyikét: 
    1. Válassza ki **... három (pont)**  az utolsó oszlopra, és válassza a **engedélyezése lemezkép**. 
    2. Válasszon ki egy vagy több lemezképet a listából válassza ki a jelölőnégyzetek a rendszerkép neve előtt a listában, és válassza ki **engedélyezése a megadott lemezképek**. 

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

## <a name="next-steps"></a>További lépések
Kezdje meg egy tesztkörnyezet létrehozását az Azure Lab Services használatával:

- [Osztályterem-tesztkörnyezet beállítása](tutorial-setup-classroom-lab.md)
- [Tesztkörnyezet beállítása](../tutorial-create-custom-lab.md)
