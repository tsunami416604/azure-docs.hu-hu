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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 3a24c826ffdd3e368b6b16f6e78fbafc50c54b1e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Hozzáférés osztályterem-tesztkörnyezethez az Azure Lab Servicesben
Ez a cikk bemutatja, hogyan férhet hozzá az osztályterem-tesztkörnyezetekhez, hogyan csatlakozhat a tesztkörnyezetben található virtuális gépekhez, és hogyan állíthatja le a virtuális gépeket. 

## <a name="view-all-the-classroom-labs"></a>Az összes osztályterem-tesztkörnyezet megtekintése

1. Keresse fel a **regisztrációs URL-címet**, amelyet a tanártól/oktatótól kapott. 
2. A regisztráció elvégzéséhez jelentkezzen be a szolgáltatásba az iskolai fiókjával. 
3. A regisztrációt követően ellenőrizze, hogy látja-e a virtuális gépeket azokban a tesztkörnyezetekben, amelyekhez hozzáférése van. 

    ![Összes tesztkörnyezet megtekintése](./media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Csatlakozás virtuális géphez osztályterem-tesztkörnyezetben

1. Válassza a **Csatlakozás** elemet azon a csempén, amely annak a tesztkörnyezetnek a virtuális gépét jelöli, amelyhez hozzá szeretne férni.

    ![Összes tesztkörnyezet megtekintése](./media/how-to-use-classroom-lab/connect-button.png)
2. A virtuális gép néhány percen belül használatra kész.

    ![A virtuális gép előkészítése a használatra](./media/how-to-use-classroom-lab/getting-virtual-machine-ready.png)
3. Mentse az RDP-fájlt a merevlemezre, majd nyissa meg. 
    
    ![Az RDP-fájl mentése](./media/how-to-use-classroom-lab/save-rdp-file.png)
4. Adja meg a **felhasználónevet** és **jelszót**, amelyet az oktatótól/tanártól kapott a gépbe való bejelentkezéshez. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Virtuális gép leállítása osztályterem-tesztkörnyezetben

Válassza a **Leállítás** lehetőséget az osztályterem-tesztkörnyezetet jelölő csempén. A virtuális gép leállítása után elérhetővé válik a csempén az **Indítás** gomb. 

## <a name="next-steps"></a>További lépések
Kezdje meg egy tesztkörnyezet létrehozását az Azure Lab Services használatával:

- [Osztályterem-tesztkörnyezet beállítása](how-to-manage-classroom-labs.md)
- [Egyéni tesztkörnyezet beállítása](tutorial-create-custom-lab.md)

 