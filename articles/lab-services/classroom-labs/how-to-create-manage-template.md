---
title: Tantermi laborsablon kezelése az Azure Lab Servicesben | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre és kezelhet tantermi laborsablont az Azure Lab Servicesben.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: fcf31fcc266358911612c25e0b73a0a9de696b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539022"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Tantermi sablon létrehozása és kezelése az Azure Lab Servicesben
A tesztkörnyezet sablonja egy alapszintű virtuálisgép-rendszerkép, amelyből az összes felhasználó virtuális gépe létrejön. Úgy állítsa be a virtuálisgép-sablont, hogy az pontosan aszerint legyen konfigurálva, amit a tesztkörnyezet felhasználóinak biztosítani kíván. A sablonhoz megadhat egy nevet és egy leírást, amely a tesztkörnyezet felhasználói számára jelenik meg. Ezután tegye közzé a sablont, hogy a sablon virtuális gép példányai elérhetővé tegyék a tesztkörnyezet felhasználói számára. Amikor közzétesz egy sablont, az Azure Lab Services létrehozza a virtuális gépeket a tesztkörnyezetben a sablon használatával. A folyamat során létrehozott virtuális gépek száma megegyezik a tesztkörnyezet felhasználóinak maximális számával, amelyet a tesztkörnyezet használati szabályzatában állíthat be. A virtuális gépek konfigurációja megegyezik a sablonéval.

Ez a cikk ismerteti, hogyan hozhat létre és kezelhet egy sablon virtuális gépet az Azure Lab Services tantermi laborjában. 

## <a name="set-or-update-template-title-and-description"></a>Sablon címének és leírásának beállítása vagy frissítése
Az alábbi lépésekkel első alkalommal állíthatja be a címet és a leírást, és később frissítheti azokat. 

1. A **Sablon** lapon adja meg a tesztkörnyezet új **címét.**  
2. Adja meg a sablon új **leírását.** Amikor a fókuszt kimozdítja a szövegdobozból, a program automatikusan menti azt. 

    ![A sablon neve és leírása](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Sablon frissítése virtuális gép
A következő lépésekkel frissítse a sablon virtuális gép.  

1. Várjon, amíg a sablon virtuális gép elindul, és válassza a **Csatlakozás sablonhoz** lehetőséget az eszköztáron a sablon virtuális géphez való csatlakozáshoz, és kövesse az utasításokat. Ha Windows-gépről van szó, megjelenik az RDP-fájl letöltésének lehetősége. 
1. Miután csatlakozott a sablonhoz, és módosításokat végzett, a továbbiakban nem lesz ugyanaz a beállítás, mint a felhasználók számára legutóbb közzétett virtuális gépek. A sablonmódosítások csak a közzététel után jelennek meg a felhasználók meglévő virtuális gépein.

    ![Csatlakozás a virtuálisgép-sablonhoz](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. Telepítse a diákok számára a tesztkörnyezet használatához szükséges szoftvereket (például Visual Studio, Azure Storage Explorer stb). 
1. Szakítsa meg a kapcsolatot a virtuálisgép-sablonnal (zárja be a távoli asztali munkamenetet). 
1. **Állítsa le** a sablon virtuális gépét a **Stop sablon kiválasztásával.** 
1. Kövesse a következő szakaszlépéseit a frissített sablon virtuális gép **ének közzétételéhez.** 

## <a name="publish-the-template-vm"></a>A virtuálisgép-sablon közzététele  
Ebben a lépésben közzé kell tennie a sablon virtuális gép. A sablon virtuális gép közzétételekor az Azure Lab Services virtuális gépeket hoz létre a laborban a sablon használatával. A virtuális gépek konfigurációja megegyezik a sablonéval.


1. A **Sablon** lapon válassza a **Közzététel** gombot az eszköztáron. 

    ![Sablon közzététele gomb](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Közzététel után a lépés nem vonható vissza. 
2. A **Közzétételi sablon** lapon adja meg a laborban létrehozni kívánt virtuális gépek számát, majd válassza a **Közzététel**lehetőséget. 

    ![Közzétételi sablon – virtuális gépek száma](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. A sablon **közzétételi állapota** a lapon. Ez a folyamat akár egy órát is igénybe vehet. 

    ![Sablon közzétételének folyamata](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Várjon, amíg a közzététel befejeződik, majd váltson a **Virtuális gépek készlet** lapra a bal oldali menü Virtuális **gépek** parancsával vagy a **Virtuális gépek** csempéjének kiválasztásával. Ellenőrizze, hogy olyan virtuális gépek jelennek-e meg, amelyek **hozzárendelés nélküli** állapotban vannak. Ezek a virtuális gépek még nincsenek diákokhoz rendelve. **Leállított** állapotban kell lenniük. Ezen a lapon indíthatja el a virtuális gépeket, csatlakozhat hozzájuk, leállíthatja, valamint törölheti őket. A virtuális gépeket elindíthatja ezen a lapon, vagy engedheti, hogy a diákjai indítsák el őket. 

    ![Leállított állapotban levő virtuális gépek](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként hozzon létre és kezeljen laborfiókokat](how-to-manage-lab-accounts.md)
- [Labortulajdonosként hozzon létre és kezeljen laborokat](how-to-manage-classroom-labs.md)
- [Labortulajdonosként konfigurálja és szabályozza a tesztkörnyezet használatát](how-to-configure-student-usage.md)
- [Laborfelhasználóként tantermi laborok elérése](how-to-use-classroom-lab.md)
