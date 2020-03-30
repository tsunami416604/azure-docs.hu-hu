---
title: Hozzáférés osztályterem-tesztkörnyezethez az Azure Lab Servicesben | Microsoft Docs
description: Ebben az oktatóanyagban megtanulhatja, hogyan férhet hozzá egy tanár által létrehozott osztályterem-tesztkörnyezetben lévő virtuális gépekhez.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: db1e60ccd7e05cb8b6923d6b2ec008cdfb76eaa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501928"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Hozzáférés osztályterem-tesztkörnyezethez az Azure Lab Servicesben
Ez a cikk ismerteti, hogyan regisztrálhat egy tantermi laborba, tekintse meg az összes elérhető laborok, indítsa el/állítsa le a virtuális gépet a laborban, és csatlakozzon a virtuális géphez. 

## <a name="register-to-the-lab"></a>Regisztráció a laborba

1. Keresse fel a **regisztrációs URL-címet**, amelyet a tanártól/oktatótól kapott. A regisztráció befejezése után nem kell használnia a regisztrációs URL-t. Ehelyett használja az [https://labs.azure.com](https://labs.azure.com)URL-t: . Az Internet Explorer 11 még nem támogatott. 
1. A regisztráció elvégzéséhez jelentkezzen be a szolgáltatásba az iskolai fiókjával. 

    > [!NOTE]
    > Az Azure Lab Services használatához Microsoft-fiók szükséges. Ha nem Microsoft-fiókjával, például Yahoo- vagy Google-fiókjával próbál bejelentkezni a portálra, kövesse az utasításokat, és hozzon létre egy Microsoft-fiókot, amely a nem Microsoft-fiókjához lesz csatolva. Ezután kövesse a lépéseket a regisztrációs folyamat befejezéséhez. 
1. A regisztrációt követően ellenőrizze, hogy látja-e a virtuális gépet abban a tesztkörnyezetben, amelyhez hozzáférése van. 
1. Várjon, amíg a virtuális gép készen áll. A virtuális gép csempéjén figyelje meg a következő mezőket:
    1. A csempe tetején megjelenik **a labor neve**.
    1. Jobbra a virtuális gép **operációs rendszerét (operációs rendszert)** jelző ikon látható. Ebben a példában ez a Windows operációs rendszer. 
    1. A csempe alján ikonok/gombok jelennek meg a virtuális gép indításához/leállításához, és csatlakozhatanak a virtuális géphez. 
    1. A gombok tól jobbra a virtuális gép állapota látható. Győződjön meg arról, hogy a virtuális gép állapota **le van állítva.**

        ![Virtuális gép leállított állapotban](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>A virtuális gép indítása vagy leállítása
1. **Indítsa el** a virtuális gép kiválasztásával az első gomb, ahogy az az alábbi képen látható. Ez a folyamat némi időt vesz igénybe.  

    ![A virtuális gép elindítása](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Ellenőrizze, hogy a virtuális gép állapota Futás állapotú.Confirm that the status of the VM is set to **Running**. 

    ![Virtuális gép futó állapotban](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Figyelje meg, hogy az első gomb ikonja úgy változott, hogy **leállítási** műveletet jelöl. Ezt a gombot a virtuális gép leállításához válassza. 

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

1. Válassza ki a második gombot, ahogy az az alábbi képen látható a labor virtuális **gépéhez való csatlakozáshoz.** 

    ![Kapcsolódás egy virtuális géphez](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Tegye az alábbi lépések egyikét: 
    1. **Windows** virtuális gépek esetén mentse az **RDP-fájlt** a merevlemezre. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Használja az oktatótól/professzortól kapott **felhasználónevet** és **jelszót** a gépre való bejelentkezéshez. 
    3. **Linuxos** virtuális gépek esetén **ssh** vagy **RDP** (ha engedélyezve van) segítségével csatlakozhat hozzájuk. További információ: [Távoli asztali kapcsolat engedélyezése Linux rendszerű gépekhez című témakörben.](how-to-enable-remote-desktop-linux.md) 
    1. Ha **Mac** et használ a tesztkörnyezet virtuális gépéhez való csatlakozáshoz, kövesse a következő szakasz utasításait. 

## <a name="progress-bar"></a>Folyamatjelző sáv 
A csempe folyamatjelző sávja a felhasznált órák számát mutatja a hozzárendelt [kvótaórák](how-to-configure-student-usage.md#set-quotas-for-users) számához képest. Ez az idő a labor ütemezett időpontja mellett az Ön számára rendelkezésre szánt további idő. A folyamatjelző és a folyamatjelző alatti szöveg színe a következő esetekszerint változik:

- Ha egy osztály folyamatban van (az osztály ütemezésén belül), a folyamatjelző szürkén jelenik meg, hogy a kvótaórákat ne használja a rendszer. 

    ![Folyamatjelző sáv szürke színnel](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Ha a kvóta nincs hozzárendelve (nulla óra), a **csak az osztályok során elérhető** szöveg jelenik meg a folyamatjelző sáv helyett. 
    
    ![Állapot, ha nincs beállítva kvóta](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Ha **elfogyott a kvóta,** a folyamatjelző sáv színe **piros.** 

    ![A folyamatjelző sáv piros színnel](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- A folyamatjelző színe **kék,** ha kívül esik a tesztkörnyezet ütemezett idején, és a kvótaidő egy részét felhasználták. 

    ![Folyamatjelző sáv kék színnel](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Az összes osztályterem-tesztkörnyezet megtekintése
Miután regisztrált a laborokba, az alábbi lépések végrehajtásával megtekintheti az összes tantermi labort: 

1. Keresse [https://labs.azure.com](https://labs.azure.com)meg a it. Az Internet Explorer 11 még nem támogatott. 
2. Jelentkezzen be a szolgáltatásba a tesztkörnyezetbe való regisztráláshoz használt felhasználói fiókhasználatával. 
3. Erősítse meg, hogy látja az összes labort, amelyhez hozzáférése van. 

    ![Összes tesztkörnyezet megtekintése](../media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként hozzon létre és kezeljen laborfiókokat](how-to-manage-lab-accounts.md)
- [Labortulajdonosként hozzon létre és kezeljen laborokat](how-to-manage-classroom-labs.md)
- [Labortulajdonosként sablonok beállítása és közzététele](how-to-create-manage-template.md)
- [Labortulajdonosként konfigurálja és szabályozza a tesztkörnyezet használatát](how-to-configure-student-usage.md)
 