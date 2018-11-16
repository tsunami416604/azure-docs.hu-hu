---
title: Tesztkörnyezet használatának nyomon követése az Azure Lab Services szolgáltatásban | Microsoft Docs
description: Ebben az oktatóanyagban egy tesztkörnyezet létrehozójaként/tulajdonosaként követjük nyomon a tesztkörnyezet használatát.
services: lab-services
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
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: 49d5761e3e37e1265938d1f1b27324de667a13ca
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51707095"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Oktatóanyag: Tesztkörnyezet használatának nyomon követése az Azure Lab Services szolgáltatásban
Az oktatóanyag bemutatja, hogyan követheti nyomon a tesztkörnyezet létrehozója/tulajdonosa a tesztkörnyezet használatát.

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * A tesztkörnyezetben regisztrált felhasználók megtekintése
> * A tesztkörnyezetben lévő virtuális gépek használati adatainak megtekintése
> * Diákok virtuális gépeinek felügyelete 


## <a name="view-users-registered-with-the-lab"></a>A tesztkörnyezetben regisztrált felhasználók megtekintése

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is.
3. A **Saját tesztkörnyezetek** lapon válassza ki azt a tesztkörnyezetet, amelynek a használatát nyomon szeretné követni. 
4. Válassza a **Felhasználók** lapot. Megjelenik a tesztkörnyezetben regisztrált felhasználók listája. Válassza a **Regisztrációs hivatkozás** lehetőséget, másolja a hivatkozást a vágólapra, és küldje el minden új tanulónak, aki még nem regisztrált a tesztkörnyezetbe. 

    ![Regisztrált felhasználók](../media/tutorial-track-usage/registered-users.png)

## <a name="view-the-usage-of-vms-in-the-lab"></a>A tesztkörnyezetben lévő virtuális gépek használati adatainak megtekintése 

1. Válassza a **Virtuális gépek** lehetőséget a bal oldali menüben. 
2. Győződjön meg róla, hogy látható a virtuális gépek állapota és az eddigi üzemórák mennyisége. A virtuális gépek nem számítanak bele a használati idő az utolsó oszlopban látható egy diák töltött idő. 

    ![Virtuálisgép-használat](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Diákok virtuális gépeinek felügyelete 
Virtuális gép egy sorra a listában, vigye az egérmutatót, mivel vezérlők a következő feladatokat végezheti el (ahogyan az előző szakaszban az ábrán látható) jelenik meg: 

- Kapcsolódás virtuális géphez
- Virtuális gép elindítása
- Virtuális gép leállítása
- Virtuális gép törlése



## <a name="next-steps"></a>További lépések
Osztályterem-tesztkörnyezetek kapcsolatos további információkért tekintse meg a cikkeket [útmutató végigvezeti a](how-to-manage-lab-accounts.md).
