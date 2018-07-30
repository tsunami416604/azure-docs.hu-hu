---
title: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással | Microsoft Docs
description: Ebben az oktatóanyagban egy tesztkörnyezetfiókot állíthat be az Azure Lab Services szolgáltatással.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: d4bfd684792e5ec13b2a4a020fa21249f1888657
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226348"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Oktatóanyag: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással
Az Azure Lab Services szolgáltatásban a tesztkörnyezetfiók központi fiókként szolgál a vállalat összes tesztkörnyezetének felügyeletéhez. A tesztkörnyezetfiókban engedélyeket adhat másoknak a tesztkörnyezetek létrehozására, és szabályzatokat állíthat be, amelyek a tesztkörnyezetfiók alá tartozó összes tesztkörnyezetben érvényesek. Ebben az oktatóanyagban megismerheti, hogyan hozhat létre tesztkörnyezetfiókot tesztkörnyezet-rendszergazdaként. 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Tesztkörnyezetfiók létrehozása
> * Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
> * A tesztkörnyezet-tulajdonosok számára elérhető rendszerképek megadása a Marketplace-en

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-lab-account"></a>Tesztkörnyezetfiók létrehozása
A következő lépések bemutatják, hogyan használhatja az Azure Portalt tesztkörnyezetfiók létrehozására az Azure Lab Services szolgáltatásban. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali főmenüben válassza az **Erőforrás létrehozása** lehetőséget.
3. Keressen a **Lab Services** szövegre az Azure Marketplace-en, és válassza a **Lab Services** alkalmazást a legördülő listából. 
4. Válassza a **Lab Services (előzetes verzió)** lehetőséget a szűrt szolgáltatáslistában. 
1. A **Tesztkörnyezetfiók létrehozása** ablakban válassza a **Létrehozás** elemet.
2. A **Tesztkörnyezetfiók** ablakban tegye a következőket: 
    1. Írjon be egy nevet a **Tesztkörnyezetfiók neve** mezőbe. 
    2. Válassza ki azt az **Azure-előfizetést**, amelyben a tesztkörnyezetfiókot létre szeretné hozni.
    3. Az **Erőforráscsoport** esetében válassza az **Új létrehozása** lehetőséget, majd adjon nevet az új erőforráscsoportnak.
    4. A **Helyszín** esetében válassza ki a helyszínt/régiót, ahol létre szeretné hozni a tesztkörnyezetfiókot. 
    5. Kattintson a **Létrehozás** gombra. 

        ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. Ha a tesztkörnyezetfiók oldala nem látható, kattintson az **Értesítések** gombra, majd az **Ugrás ide: erőforrás** gombra az értesítésekben. 

    ![Tesztkörnyezetfiók létrehozása ablak](../media/tutorial-setup-lab-account/notification-go-to-resource.png)    
6. Megjelenik az alábbi **tesztkörnyezetfiók** oldala:

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz
A felhasználónak **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. Ha ugyanazt a felhasználói fiókot tervezi használni az osztályterem-tesztkörnyezet létrehozásához, ezt a lépést kihagyhatja. Ha egy másik felhasználói fiókot tervez használni, kövesse a következő lépéseket: 

Ha engedélyt kíván adni az oktatóknak, hogy létrehozzák a tesztkörnyezeteket a tanóráikhoz, adja hozzá őket a **Tesztkörnyezet-létrehozó** szerepkörhöz:

1. A **Tesztkörnyezetfiók** oldalon válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget, majd kattintson az eszköztár **+ Hozzáadás** elemére. 

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/access-control.png)
2. Az **Engedélyek hozzáadása** oldal **Szerepkör** területén válassza a **Tesztkörnyezet-létrehozó** lehetőséget, válassza ki a Tesztkörnyezet-létrehozó szerepkörhöz hozzáadni kívánt felhasználót, majd kattintson a **Mentés** gombra. 

    ![Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)

## <a name="specify-marketplace-images-available-to-lab-owners"></a>A tesztkörnyezet-tulajdonosok számára elérhető rendszerképek megadása a Marketplace-en
Ebben a szakaszban azokat a Marketplace-beli rendszerképeket fogja megadni, amelyeket a tesztkörnyezet-tulajdonosok az osztályterem-tesztkörnyezetek létrehozásához használhatnak. 

1. Válassza a bal oldali menüből a **Marketplace-beli rendszerképek** elemet. Alapértelmezés szerint a rendszerképek teljes listája jelenik meg (az engedélyezett és a letiltott rendszerképek egyaránt). A fenti legördülő listában a **Csak az engedélyezettek**/**Csak a letiltottak** lehetőségre kattintva szűrheti a listát, hogy csak az engedélyezett/letiltott rendszerképek jelenjenek meg. 

    ![Marketplace-beli rendszerképek oldal](../media/tutorial-setup-lab-account/marketplace-images-page.png)
2. Egy engedélyezett Marketplace-beli rendszerkép **letiltásának** a következő módjai vannak: 
    1. Kattintson az utolsó oszlopban a három pontra **(...)**, majd a **Rendszerkép letiltása** lehetőségre. 

        ![Egy rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Jelöljön ki egy vagy több, a listában szereplő rendszerképet a nevük előtti jelölőnégyzet kijelölésével, majd kattintson a **Kiválasztott rendszerképek letiltása** lehetőségre. 

        ![Több rendszerkép letiltása](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. A letiltott Marketplace-beli rendszerképek **engedélyezésének** a fentiekhez hasonlóan a következő módjai vannak: 
    1. Kattintson az utolsó oszlopban a három pontra **(...)**, majd a **Rendszerkép engedélyezése** lehetőségre. 
    2. Jelöljön ki egy vagy több, a listában szereplő rendszerképet a nevük előtti jelölőnégyzet kijelölésével, majd kattintson a **Kiválasztott rendszerképek engedélyezése** lehetőségre. 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy tesztkörnyezetfiókot. Ha meg szeretné tudni, tanárként hogyan hozhat létre osztályterem-tesztkörnyezetet, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Osztályterem-tesztkörnyezet beállítása](tutorial-setup-classroom-lab.md)

