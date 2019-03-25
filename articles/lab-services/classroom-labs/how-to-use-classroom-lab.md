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
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 8e20f612bc54433091036377d51a7e59e3abec51
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402150"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Hozzáférés osztályterem-tesztkörnyezethez az Azure Lab Servicesben
Ez a cikk bemutatja, hogyan férhet hozzá az osztályterem-tesztkörnyezetekhez, hogyan csatlakozhat a tesztkörnyezetben található virtuális gépekhez, és hogyan állíthatja le a virtuális gépeket. 

## <a name="register-to-a-lab"></a>Regisztráljon egy laborhoz
1. Keresse fel a **regisztrációs URL-címet**, amelyet a tanártól/oktatótól kapott. 
2. A regisztráció elvégzéséhez jelentkezzen be a szolgáltatásba az iskolai fiókjával. 
3. A regisztrációt követően ellenőrizze, hogy látja-e a virtuális gépet abban a tesztkörnyezetben, amelyhez hozzáférése van. 
2. Várja meg, amíg a virtuális gép készen áll, majd **indítsa el** azt. Ez a folyamat hosszabb időt vesz igénybe.  

    ![A virtuális gép elindítása](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)


## <a name="view-all-the-classroom-labs"></a>Az összes osztályterem-tesztkörnyezet megtekintése
Miután regisztrálta a labs, az osztályterem-tesztkörnyezetek tekintheti meg a következő lépések végrehajtásával: 

1. Navigáljon a [ https://labs.azure.com ](https://labs.azure.com). 
2. Jelentkezzen be a szolgáltatásba a tesztkörnyezetben való regisztrálásához használt felhasználói fiókkal. 
3. Győződjön meg arról, hogy megjelenik-e hozzáféréssel rendelkezik az összes labs. 

    ![Összes tesztkörnyezet megtekintése](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Csatlakozás virtuális géphez osztályterem-tesztkörnyezetben

1. A virtuális gép Ha még nem indult el, válassza ki a kezdési **Start** a csempére. 
2. Válassza a **Csatlakozás** elemet azon a csempén, amely annak a tesztkörnyezetnek a virtuális gépét jelöli, amelyhez hozzá szeretne férni. 
3. Hajtsa végre a következő lépések egyikét: 
   1. A **Windows** virtuális gépeket, mentse a **RDP** a merevlemez-fájlt. Nyissa meg a virtuális géphez csatlakozni RDP-fájlt. Használja a **felhasználónév** és **jelszó** jelentkezzen be a gép az oktató/professzor kérhet. 
   3. A **Linux** virtuális gépeket, másolja és menti az SSH-kapcsolati karakterláncot a **csatlakozhat a virtuális gép** párbeszédpanel bezárásához. Használja ezt a kapcsolati karakterláncot egy SSH-terminálból (például [Putty](https://www.putty.org/)) a virtuális gép csatlakozni.

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Virtuális gép leállítása osztályterem-tesztkörnyezetben

A virtuális gép leállításához válassza ki **leállítása** a csempére. A virtuális gép leállítása után elérhetővé válik a csempén az **Indítás** gomb. 

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként hozzon létre, és tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)
- [Labortulajdonosként hozzon létre és laborok kezelése](how-to-manage-classroom-labs.md)
- [Labortulajdonosként állítsa be, és a sablonok közzététele](how-to-create-manage-template.md)
- [Labortulajdonosként konfigurálása, és a egy lab használatának szabályozása](how-to-configure-student-usage.md)
 