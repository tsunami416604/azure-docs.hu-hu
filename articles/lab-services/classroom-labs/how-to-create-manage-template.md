---
title: Osztályterem-tesztkörnyezet, az Azure Lab Services sablonját kezelése |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és kezelhet az Azure Lab Services osztályterem laborsablon.
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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: d0942d3465811687937ce113e664fbf3f91277d0
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55815244"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Létrehozása és kezelése az Azure Lab Services osztályterem-sablonból
A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Úgy állítsa be a virtuálisgép-sablont, hogy az pontosan aszerint legyen konfigurálva, amit a tesztkörnyezet felhasználóinak biztosítani kíván. A sablonhoz megadhat egy nevet és egy leírást, amely a tesztkörnyezet felhasználói számára jelenik meg. Ezt követően a sablont a Virtuálisgép-sablon példányainak a labor számára elérhetővé tehet közzé. Amikor közzétesz egy sablont, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval.

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet az Azure Lab Services osztályterem-tesztkörnyezet sablon virtuális gépeken. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Osztályterem-tesztkörnyezet létrehozása során a sablon közzététele
Először állítsa be, és a sablon közzététele osztályterem-tesztkörnyezet létrehozása során.

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is. 
3. Az **Új tesztkörnyezet** ablakban tegye a következőket: 
    1. Adjon egy **nevet** a tesztkörnyezetnek. 
    2. Adja meg azoknak a **felhasználóknak a maximális számát**, akik használhatják a tesztkörnyezetet. 
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
6. A **Sablon konfigurálása** oldalon látható a tesztkörnyezet létrehozási folyamatának állapota. A sablon létrehozása a tesztkörnyezetben akár 20 percig is eltarthat. 

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
    1. A sablon azonnali közzétételéhez jelölje be a következő figyelmeztetés jelölőnégyzetét: *Megértettem, hogy közzététel után nem lehet módosítani a sablont. A folyamatot csak egyszer lehet elvégezni, és akár egy órát is igénybe vehet*, majd válassza ki a **Közzététel** gombot.  

        > [!WARNING]
        > Közzététel után a lépés nem vonható vissza. 
    2. Későbbi közzétételhez kattintson a **Mentés későbbre** gombra. A varázsló futtatása után közzéteheti a virtuálisgép-sablont. Konfigurálása és közzététele a varázsló befejezése után információt talál arról, hogyan konfigurálása és közzététele a varázsló befejezése után, lásd: Közzététel a sablon című rész a [osztályterem-tesztkörnyezetek kezelése](how-to-manage-classroom-labs.md) cikk.

        ![Sablon közzététele](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Nyomon követheti, hogy **hol tart a sablon közzététele**. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Miután a sablon közzététele sikeresen befejeződött, az alábbi oldalt fogja látni. Válassza a **Done** (Kész) lehetőséget.

    ![Sablon sikeres közzététele](../media/tutorial-setup-classroom-lab/publish-success.png)
1. A rendszer megjeleníti a tesztkörnyezet **irányítópultját**. 
    
    ![Az osztályterem-tesztkörnyezet irányítópultja](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

 
## <a name="set-or-update-template-title-and-description"></a>Állítsa be, vagy frissítse a sablon nevét és leírását
Az alábbi lépéseket követve cím és leírás, először állítsa be, és később frissíteni. 

1. Az a **sablon** részben, vigye az egérmutatót **neve** -sablon vagy **leírás** a sablon, és jelölje ki. 
2. Adja meg a **új nevet** vagy **új leírást** a sablont, majd nyomja le az **ENTER**.

    ![Templae neve és leírása](../media/how-to-create-manage-template/template-name-description.png)

## <a name="set-up-or-update-a-template-vm"></a>Állítsa be, vagy egy Virtuálisgép-sablon frissítéséhez
 A virtuálisgép-sablonhoz csatlakozva telepítse a szükséges szoftvereket, mielőtt elérhetővé tenné a sablont a diákok számára. Kövesse az alábbi lépéseket egy sablont, virtuális gép beállításához először, vagy frissítse a virtuális Gépet. 

1. Várja meg, amíg elkészül a virtuálisgép-sablon. Ha elkészült, elérhetővé válik a **Start** gomb. A virtuális gép elindításához kattintson a **Start** gombra.

    ![A virtuálisgép-sablon elindítása](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Tekintse át a figyelmeztető üzenetet, és válassza ki **Start**. 

    ![Indítsa el a sablon – figyelmeztetés](../media/how-to-create-manage-template/start-template-warning.png)
1. Miután elindul, a virtuális Géphez való csatlakozáshoz válassza **Connect**, és kövesse az utasításokat. 
1. Telepítse a diákok számára a tesztkörnyezet használatához szükséges szoftvereket (például Visual Studio, Azure Storage Explorer stb). 
2. Szakítsa meg a kapcsolatot a virtuálisgép-sablonnal (zárja be a távoli asztali munkamenetet). 
3. **Állítsa le** a virtuálisgép-sablont a **Leállítás** kiválasztásával. 

## <a name="publish-the-template-vm"></a>A virtuálisgép-sablon közzététele  
Ha a labor létrehozása közben nem tesz közzé a sablont, később is közzéteheti. A közzététel előtt érdemes a sablont, virtuális gép kapcsolódni, és frissítse olyan szoftvert. Amikor közzétesz egy sablont, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval. 

1. A **Sablon** szakaszban válassza a **Közzététel** lehetőséget. 

    ![A virtuálisgép-sablon közzététele](../media/tutorial-setup-classroom-lab/public-access.png)
1. Az a **a sablon közzététele** üzenetpanelen, tekintse át az üzenetet, és válassza ki **közzététel**. Ez a folyamat eltarthat egy ideig, attól függően, hogy hány virtuális gépet hoznak létre.
    
    > [!IMPORTANT]
    > Miután a sablon közzé lett téve, a közzétételt nem lehet visszavonni. A sablon, ha újbóli. 
4. Várja meg, módosítsa a sablon állapota **közzétett**. 

    ![Közzétételi állapot](../media/how-to-create-manage-template/publish-status.png)
1. Váltson a **Virtuális gépek** lapra, és ellenőrizze, hogy vannak-e ott **Nem hozzárendelt** állapotú virtuális gépek. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. Várja meg, amíg befejeződik a virtuális gépek létrehozása. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. Ezen a lapon elindíthatja őket, vagy lehetővé teszik a diákjai, indítsa el a virtuális gépeket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>A sablon újbóli közzététele 
A sablon a közzététel után továbbra is csatlakozhat a virtuális gép sablon, frissíteni és majd újból közzé kell tennie. Ha ismét közzéteszi a sablon virtuális Gépet, az összes felhasználói első leválasztása a virtuális gépek és azok létrejönnek a frissített sablon alapján. 

1. A labor irányítópult oldalon válassza ki a **tegye közzé újra** a sablon szakaszban. 

    ![Tegye közzé újra gomb](../media/how-to-create-manage-template/republish-button.png)
2. Az a **tegye közzé újra a sablont** üzenetpanelen, tekintse át a szöveget, majd válassza **tegye közzé újra** folytatásához. Ellenkező esetben válassza **Mégse**. 

    ![Tegye közzé újra a sablont üzenet](../media/how-to-create-manage-template/republish-template-message.png)
3. Akkor a közzététel állapotát jelenik meg a csempét a **sablon** szakaszban.

    ![Állapot annak újbóli közzétételét](../media/how-to-create-manage-template/republish-status-publishing.png)
4. A sablon közzététele után az állapot értéke **közzétett**. 

    ![Sikeres közzététel](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként hozzon létre, és tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)
- [Labortulajdonosként hozzon létre és laborok kezelése](how-to-manage-classroom-labs.md)
- [Labortulajdonosként konfigurálása, és a egy lab használatának szabályozása](how-to-configure-student-usage.md)
- [Labor felhasználóként osztályterem-tesztkörnyezetek elérése](how-to-use-classroom-lab.md)
