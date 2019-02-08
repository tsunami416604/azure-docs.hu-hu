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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 28710ebfaef4c82212e0208c36f50a96f0dda083
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892048"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>Oktatóanyag: Az Azure Lab Services tesztkörnyezetfiók beállítása
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

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy tesztkörnyezetfiókot. Ha meg szeretné tudni, tanárként hogyan hozhat létre osztályterem-tesztkörnyezetet, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Osztályterem-tesztkörnyezet beállítása](tutorial-setup-classroom-lab.md)

