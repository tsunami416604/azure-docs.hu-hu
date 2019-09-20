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
ms.date: 09/19/2019
ms.author: spelluru
ms.openlocfilehash: cf1bd223c852db930835ea1b56a93a381e8312c5
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71161423"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Oktatóanyag: Tantermi labor elérése Azure Lab Services
Ebben az oktatóanyagban a diákok megtanulnak csatlakozni egy osztályterem-tesztkörnyezet virtuális gépéhez (VM). 

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Regisztráció a laborba
> * A virtuális gép elindítása
> * Kapcsolódás a virtuális géphez

## <a name="register-to-the-lab"></a>Regisztráció a laborba

1. Keresse fel a **regisztrációs URL-címet**, amelyet a tanártól/oktatótól kapott. A regisztráció befejezése után nem szükséges a regisztrációs URL-cím használata. Ehelyett használja az URL-címet [https://labs.azure.com](https://labs.azure.com):. Az Internet Explorer 11 még nem támogatott. 
1. A regisztráció elvégzéséhez jelentkezzen be a szolgáltatásba az iskolai fiókjával. 

    > [!NOTE]
    > A Azure Lab Services használatához Microsoft-fiók szükséges. Ha nem Microsoft-fiók, például a Yahoo vagy a Google fiókok használatával próbál bejelentkezni a portálra, kövesse az utasításokat, és hozzon létre egy Microsoft-fiók, amely a nem Microsoft-fiókhoz lesz társítva. Ezután kövesse a lépéseket a regisztrációs folyamat befejezéséhez. 
1. A regisztrációt követően ellenőrizze, hogy látja-e a virtuális gépet abban a tesztkörnyezetben, amelyhez hozzáférése van. 
1. Várjon, amíg a virtuális gép készen áll. A virtuális gép csempén figyelje meg a következő mezőket:
    1. A csempe tetején megjelenik a **labor neve**.
    1. A jobb oldalon megjelenik a virtuális gép **operációs rendszerét** jelképező ikon. Ebben a példában ez a Windows operációs rendszer. 
    1. A csempe folyamatjelzője megjeleníti az órák számát, amelyet a rendszer az Ön által hozzárendelt [kvóta-órák](how-to-configure-student-usage.md#set-quotas-for-users) száma alapján használ. Ez az idő az a további időpont, amelyet a tesztkörnyezet ütemezett ideje mellett is kiosztottak. 
    1. A csempe alján található ikonokat/gombokat a virtuális gép elindításához/leállításához és a virtuális géphez való kapcsolódáshoz használhatja. 
    1. A gombok jobb oldalán megtekintheti a virtuális gép állapotát. Győződjön meg arról, hogy a virtuális gép állapota **leállt**. 

        ![Leállított állapotú virtuális gép](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>A virtuális gép elindítása
1. **Indítsa el** a virtuális gépet az alábbi képen látható első gomb kiválasztásával. Ez a folyamat hosszabb időt vesz igénybe.  

    ![A virtuális gép elindítása](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Győződjön meg arról, hogy a virtuális gép állapota **fut**értékre van állítva. 

    ![Futó állapotú virtuális gép](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Figyelje meg, hogy az első gomb ikonja egy **leállítási** műveletet jelöl. Ezzel a gombbal állíthatja le a virtuális gépet. 

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

1. A labor virtuális géphez való **kapcsolódáshoz** kattintson a következő képen látható második gombra. 

    ![Kapcsolódás egy virtuális géphez](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Hajtsa végre az alábbi lépések egyikét: 
    1. **Windows rendszerű** virtuális gépek esetén mentse az **RDP** -fájlt a merevlemezre. Nyissa meg az RDP-fájlt a virtuális géphez való kapcsolódáshoz. Használja az oktató/professzor által a gépre való bejelentkezéshez kapott felhasználónevet és **jelszót** . 
    3. **Linux** rendszerű virtuális gépek esetén az **SSH** vagy az **RDP** (ha engedélyezve van) használatával csatlakozhat hozzájuk. További információ: [Távoli asztali kapcsolat engedélyezése Linux rendszerű gépekhez](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanult hozzáférni egy osztályterem-tesztkörnyezethez az oktatótól/tanártól kapott regisztrációs hivatkozás használatával.

A labor tulajdonosaként szeretné megtekinteni, hogy ki regisztrált a laborban, és nyomon követheti a virtuális gépek használatát. Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan követheti nyomon a labor használatát:

> [!div class="nextstepaction"]
> [Tesztkörnyezet használatának nyomon követése](tutorial-track-usage.md) 
