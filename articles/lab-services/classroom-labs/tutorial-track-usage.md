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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9408ae20246d7a48340434c201c4568bc02719cd
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54390490"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Oktatóanyag: A labor létrehozása az Azure Lab Service használatának nyomon követése
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
4. Válassza ki **felhasználók** a bal oldali menüben, vagy **felhasználók** csempére. Megjelenik a tesztkörnyezetben regisztrált felhasználók listája. Válassza a **Regisztrációs hivatkozás** lehetőséget, másolja a hivatkozást a vágólapra, és küldje el minden új tanulónak, aki még nem regisztrált a tesztkörnyezetbe. 

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

Gombok segítségével indítása, leállítása vagy törlése egy virtuális Gépet. 



## <a name="next-steps"></a>További lépések
Osztályterem-tesztkörnyezetek kapcsolatos további információkért tekintse meg a cikkeket [útmutató végigvezeti a](how-to-manage-lab-accounts.md).
