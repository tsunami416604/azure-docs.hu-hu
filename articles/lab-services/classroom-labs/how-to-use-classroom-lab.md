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
ms.date: 11/15/2018
ms.author: spelluru
ms.openlocfilehash: b5a55a35dc4baacf8248adad3133220214050eeb
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706687"
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

1. A virtuális gép Ha még nem indult el, válassza ki a kezdési **Start**.
2. Válassza a **Csatlakozás** elemet azon a csempén, amely annak a tesztkörnyezetnek a virtuális gépét jelöli, amelyhez hozzá szeretne férni. 

    ![Összes tesztkörnyezet megtekintése](../media/how-to-use-classroom-lab/connect-button.png)
3. Mentse az RDP-fájlt (a Windows VM-EK) a merevlemezen, és nyissa meg. 
4. Adja meg a **felhasználónevet** és **jelszót**, amelyet az oktatótól/tanártól kapott a gépbe való bejelentkezéshez. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Virtuális gép leállítása osztályterem-tesztkörnyezetben

A virtuális gép leállításához válassza ki **leállítása** a csempére. A virtuális gép leállítása után elérhetővé válik a csempén az **Indítás** gomb. 

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [Rendszergazdaként hozzon létre, és tesztkörnyezetfiókok kezelése](how-to-manage-lab-accounts.md)
- [Labortulajdonosként hozzon létre és laborok kezelése](how-to-manage-classroom-labs.md)
- [Labortulajdonosként állítsa be, és a sablonok közzététele](how-to-create-manage-template.md)
- [Labortulajdonosként konfigurálása, és a egy lab használatának szabályozása](how-to-configure-student-usage.md)
 