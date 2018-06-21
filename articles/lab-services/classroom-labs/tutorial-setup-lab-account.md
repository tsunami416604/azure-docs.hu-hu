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
ms.openlocfilehash: 600be7518bc526d3f147bb16377677854b676f63
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823129"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Oktatóanyag: Tesztkörnyezetfiók beállítása az Azure Lab Services szolgáltatással
Az Azure Lab Services szolgáltatásban a tesztkörnyezetfiók központi fiókként szolgál a vállalat összes tesztkörnyezetének felügyeletéhez. A tesztkörnyezetfiókban engedélyeket adhat másoknak a tesztkörnyezetek létrehozására, és szabályzatokat állíthat be, amelyek a tesztkörnyezetfiók alá tartozó összes tesztkörnyezetben érvényesek. Ebben az oktatóanyagban megismerheti, hogyan hozhat létre tesztkörnyezetfiókot tesztkörnyezet-rendszergazdaként. 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Tesztkörnyezetfiók létrehozása
> * Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz

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
Ha engedélyt kíván ad az oktatóknak, hogy létrehozzák a tesztkörnyezeteket a tanóráikhoz, adja hozzá őket a Tesztkörnyezet-létrehozó szerepkörhöz:

1. A **Tesztkörnyezetfiók** oldalon válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget, majd kattintson az eszköztár **+ Hozzáadás** elemére. 

    ![Tesztkörnyezetfiók-oldal](../media/tutorial-setup-lab-account/access-control.png)
2. Az **Engedélyek hozzáadása** oldal **Szerepkör** területén válassza a **Tesztkörnyezet-létrehozó** lehetőséget, válassza ki a Tesztkörnyezet-létrehozó szerepkörhöz hozzáadni kívánt felhasználót, majd kattintson a **Mentés** gombra. 

    ![Felhasználó hozzáadása a Tesztkörnyezet-létrehozó szerepkörhöz](../media/tutorial-setup-lab-account/add-user-to-lab-creator-role.png)


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy tesztkörnyezetfiókot. Ha meg szeretné tudni, tanárként hogyan hozhat létre osztályterem-tesztkörnyezetet, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Osztályterem-tesztkörnyezet beállítása](tutorial-setup-classroom-lab.md)

