---
title: Hozzáférés egy osztályterem-tesztkörnyezethez az Azure Lab Services szolgáltatásban | Microsoft Docs
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
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 27d79e28a986e929fb71dd77fc50b3c2cd32618f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77134039"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Oktatóanyag: Hozzáférés egy osztályterem-tesztkörnyezethez az Azure Lab Services szolgáltatásban
Ebben az oktatóanyagban a diákok megtanulnak csatlakozni egy osztályterem-tesztkörnyezet virtuális gépéhez (VM). 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Regisztráció a laborba
> * A virtuális gép elindítása
> * Kapcsolódás a virtuális géphez

## <a name="register-to-the-lab"></a>Regisztráció a laborba

1. Keresse fel a **regisztrációs URL-címet**, amelyet a tanártól/oktatótól kapott. A regisztráció befejezése után nem kell használnia a regisztrációs URL-t. Ehelyett használja az [https://labs.azure.com](https://labs.azure.com)URL-t: . Az Internet Explorer 11 még nem támogatott. 
1. A regisztráció elvégzéséhez jelentkezzen be a szolgáltatásba az iskolai fiókjával. 

    > [!NOTE]
    > Az Azure Lab Services használatához Microsoft-fiók szükséges. Ha nem Microsoft-fiókjával, például Yahoo- vagy Google-fiókjával próbál bejelentkezni a portálra, kövesse az utasításokat, és hozzon létre egy Microsoft-fiókot, amely a nem Microsoft-fiókjához lesz csatolva. Ezután kövesse a lépéseket a regisztrációs folyamat befejezéséhez. 
1. A regisztrációt követően ellenőrizze, hogy látja-e a virtuális gépet abban a tesztkörnyezetben, amelyhez hozzáférése van. 
1. Várjon, amíg a virtuális gép készen áll. A virtuális gép csempéjén figyelje meg a következő mezőket:
    1. A csempe tetején megjelenik **a labor neve**.
    1. Jobbra a virtuális gép **operációs rendszerét (operációs rendszert)** jelző ikon látható. Ebben a példában ez a Windows operációs rendszer. 
    1. A csempe folyamatjelző sávja a felhasznált órák számát mutatja a hozzárendelt [kvótaórák](how-to-configure-student-usage.md#set-quotas-for-users) számához képest. Ez az idő a labor ütemezett időpontja mellett az Ön számára rendelkezésre szánt további idő. 
    1. A csempe alján ikonok/gombok jelennek meg a virtuális gép indításához/leállításához, és csatlakozhatanak a virtuális géphez. 
    1. A gombok tól jobbra a virtuális gép állapota látható. Győződjön meg arról, hogy a virtuális gép állapota **le van állítva.** 

        ![Virtuális gép leállított állapotban](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>A virtuális gép elindítása
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

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanult hozzáférni egy osztályterem-tesztkörnyezethez az oktatótól/tanártól kapott regisztrációs hivatkozás használatával.

A tesztkörnyezet tulajdonosaként szeretné megtekinteni, hogy ki regisztrált a laborban, és nyomon követheti a virtuális gépek használatát. Haladjon tovább a következő oktatóanyaghoz, és ismerje meg, hogyan követheti nyomon a labor használatát:

> [!div class="nextstepaction"]
> [Tesztkörnyezet használatának nyomon követése](tutorial-track-usage.md) 
