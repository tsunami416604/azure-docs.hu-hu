---
title: Az Azure Lab Services osztályterem-tesztkörnyezetek kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és konfigurálja az osztályterem-tesztkörnyezet, megtekintheti az összes az osztályterem-tesztkörnyezetek, a regisztráció hivatkozásra a lab-felhasználó vagy törlése a labor fájlmegosztás.
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
ms.date: 01/16/2019
ms.author: spelluru
ms.openlocfilehash: 9576854fbad88724f3d229d239b9d3e263fb7492
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54912401"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Az Azure Lab Services osztályterem-tesztkörnyezetek kezelése 
Ez a cikk azt ismerteti, hogyan hozhat létre, és törölje az osztályterem-tesztkörnyezet. Bemutatja azt is, az osztályterem-tesztkörnyezetek megtekintése a lab-fiókokban. 

## <a name="prerequisites"></a>Előfeltételek
A **Tesztkörnyezet-létrehozó** szerepkörrel kell rendelkeznie a tesztkörnyezetfiókban ahhoz, hogy létrehozhasson egy osztályterem-tesztkörnyezetet. A tesztkörnyezetfiók létrehozásához használt fiók automatikusan hozzáadódik ehhez a szerepkörhöz. A lab tulajdonosa az a következő cikkben szereplő lépések segítségével a tesztkörnyezet létrehozója szerepkör adhat hozzá más felhasználók: [Felhasználó hozzáadása a tesztkörnyezet létrehozója szerepkör](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). 
2. Válassza ki **jelentkezzen be a**. Válasszon vagy adjon meg egy **felhasználói azonosító** , amely tagja a **tesztkörnyezet létrehozója** a laborkörnyezetben található szerepkör-fiókja, majd adja meg a jelszót. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon egy **nevet** a tesztkörnyezetnek. 
    2. Adja meg a maximális **virtuális gépek száma** a tesztkörnyezetben. Növelheti vagy csökkentheti a később a laborkörnyezetben található virtuális gépek számát. 
    6. Kattintson a **Mentés** gombra.

        ![Osztályterem-tesztkörnyezet létrehozása](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. A **virtuális gép specifikációinak kiválasztására** szolgáló lapon hajtsa végre a következőket:
    1. Válasszon **méretet** a tesztkörnyezetben létrehozott virtuális gépeknek. 
    2. Válassza ki a **régiót**, amelyben létre szeretné hozni a virtuális gépeket. 
    3. Válassza ki a tesztkörnyezetben a virtuális gépek létrehozásához használni kívánt **virtuálisgép-rendszerképet**. 
    4. Kattintson a **Tovább** gombra.

        ![Virtuális gép specifikációinak megadása](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. A **Hitelesítő adatok beállítása** oldalon adja meg az alapértelmezett hitelesítő adatokat a tesztkörnyezet összes virtuális gépéhez. 
    1. Adja meg a **felhasználónevet** a tesztkörnyezet összes virtuális gépéhez.
    2. Adja meg a felhasználó **jelszavát**. 

        > [!IMPORTANT]
        > Jegyezze fel a felhasználónevet és a jelszót, mert többször nem fognak megjelenni.
    3. Kattintson a **Létrehozás** gombra. 

        ![Hitelesítő adatok beállítása](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. A **Sablon konfigurálása** oldalon látható a tesztkörnyezet létrehozási folyamatának állapota. A sablon létrehozása a tesztkörnyezetben akár 20 percig is eltarthat. A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Úgy állítsa be a virtuálisgép-sablont, hogy az pontosan aszerint legyen konfigurálva, amit a tesztkörnyezet felhasználóinak biztosítani kíván.  

    ![Sablon konfigurálása](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Miután a sablon konfigurálása befejeződött, az alábbi oldal jelenik meg: 

    ![Sablon konfigurálása oldal a folyamat befejeződése után](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Az oktatóanyag alábbi lépéseit nem kötelező végrehajtani: 
    1. Az **Indítás** gomb kiválasztásával indítsa el a virtuálisgép-sablont.
    2. A **Csatlakozás** gomb kiválasztásával csatlakozzon a virtuálisgép-sablonhoz. 
    3. Telepítsen és konfiguráljon szoftvert a virtuálisgép-sablonon. 
    4. **Állítsa le** a virtuális gépet.  
    5. Adja meg a sablon **leírását**.

        ![A Tovább lépés a Sablon konfigurálása oldalon](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Válassza ki a **Tovább** gombot a sablon oldalán. 
10. A **Sablon közzététele** oldalon tegye az alábbiakat. 
    1. A sablon azonnali közzétételéhez jelölje be a következő figyelmeztetés jelölőnégyzetét: *Megértettem, hogy közzététel után nem lehet módosítani a sablont. A folyamatot csak egyszer lehet elvégezni, és akár egy órát is igénybe vehet*, majd válassza ki a **Közzététel** gombot.  A sablon közzétételével a tesztkörnyezet felhasználói számára elérhető példányokat hozhat létre a virtuálisgép-sablonról.

        > [!WARNING]
        > Közzététel után a lépés nem vonható vissza. 
    2. Későbbi közzétételhez kattintson a **Mentés későbbre** gombra. A varázsló futtatása után közzéteheti a virtuálisgép-sablont. Hogyan konfigurálása és közzététele a varázsló befejezése után, konfigurálása és közzététele a varázsló befejezése után információt talál a részletekért lásd: [a sablon közzététele](#publish-the-template) című rész a [osztályterem-tesztkörnyezetek kezelése ](how-to-manage-classroom-labs.md) cikk.

        ![Sablon közzététele](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Nyomon követheti, hogy **hol tart a sablon közzététele**. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Miután a sablon közzététele sikeresen befejeződött, az alábbi oldalt fogja látni. Válassza a **Done** (Kész) lehetőséget.

    ![Sablon sikeres közzététele](../media/tutorial-setup-classroom-lab/publish-success.png)
1. A rendszer megjeleníti a tesztkörnyezet **irányítópultját**. 
    
    ![Az osztályterem-tesztkörnyezet irányítópultja](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Váltson a **Virtuális gépek** lapra, és ellenőrizze, hogy vannak-e ott **Nem hozzárendelt** állapotú virtuális gépek. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="view-all-classroom-labs"></a>Osztályterem-tesztkörnyezetek megtekintése
1. Navigáljon a [Azure Lab Services portálon](https://labs.azure.com).
2. Válassza ki **jelentkezzen be a**. Válasszon vagy adjon meg egy **felhasználói azonosító** , amely tagja a **tesztkörnyezet létrehozója** a laborkörnyezetben található szerepkör-fiókja, majd adja meg a jelszót. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Győződjön meg arról, hogy megjelenik-e a kiválasztott labor-fiókban lévő összes labs. 

    ![Az összes labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Az oldal tetején a legördülő lista segítségével válassza ki a különböző tesztkörnyezetfiók. A kiválasztott labor fiókban labs láthatja. 

## <a name="delete-a-classroom-lab"></a>Osztályterem-tesztkörnyezet törlése
1. A tesztkörnyezethez a csempére válassza a három pontra (...) sarokban. 

    ![A tesztkörnyezet kiválasztása](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Válassza a **Törlés** elemet. 

    ![Törlés gomb](../media/how-to-manage-classroom-labs/delete-button.png)
3. Az a **Delete lab** párbeszédpanelen jelölje ki **törlése**. 

    ![Törlése párbeszédpanel](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Labortulajdonosként állítsa be, és a sablonok közzététele](how-to-create-manage-template.md)
- [Labortulajdonosként konfigurálása, és a egy lab használatának szabályozása](how-to-configure-student-usage.md)
- [Labor felhasználóként osztályterem-tesztkörnyezetek elérése](how-to-use-classroom-lab.md)

