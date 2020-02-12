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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 55c7e2f30324fe7878e38f1129c63c6e0c51a75f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134826"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Oktatóanyag: Tesztkörnyezet használatának nyomon követése az Azure Lab Services szolgáltatásban
Az oktatóanyag bemutatja, hogyan követheti nyomon a tesztkörnyezet létrehozója/tulajdonosa a tesztkörnyezet használatát.

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * A tesztkörnyezetben regisztrált felhasználók megtekintése
> * A tesztkörnyezetben lévő virtuális gépek használati adatainak megtekintése
> * Diákok virtuális gépeinek felügyelete 


## <a name="view-registered-users"></a>Regisztrált felhasználók megtekintése

1. Lépjen az [Azure Lab Services weboldalára](https://labs.azure.com). 
2. Válassza a **Bejelentkezés** lehetőséget, és adja meg a hitelesítő adatait. Az Azure Lab Services támogatja a szervezeti fiókok és a Microsoft-fiókok használatát is.
3. A **Saját tesztkörnyezetek** lapon válassza ki azt a tesztkörnyezetet, amelynek a használatát nyomon szeretné követni. 
4. A bal oldali menüben vagy a **felhasználók** csempén válassza a **felhasználók** lehetőséget. Megjelenik a tesztkörnyezetben regisztrált felhasználók listája.  

    ![Regisztrált felhasználók](../media/tutorial-track-usage/registered-users.png)

    A tesztkörnyezet felhasználói hozzáadásával és kezelésével kapcsolatos további információkért lásd: [labor-felhasználók hozzáadása és kezelése](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms"></a>Virtuális gépek használatának megtekintése

1. Válassza a **Virtuális gépek** lehetőséget a bal oldali menüben. 
2. Győződjön meg róla, hogy látható a virtuális gépek állapota és az eddigi üzemórák mennyisége. Az az idő, ameddig a labor tulajdonosa egy tanuló virtuális gépen elkölt, nem számít az utolsó oszlopban látható használati idő függvényében. 

    ![Virtuálisgép-használat](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Diákok virtuális gépeinek felügyelete 
Ezen az oldalon az **állapot** oszlopban vagy az eszköztáron található vezérlők használatával indíthatja el, állíthatja le vagy állíthatja alaphelyzetbe a tanulói virtuális gépeket.

![Virtuális gép vezérlői](../media/tutorial-track-usage/vm-controls.png)

A virtuálisgép-készlet tesztkörnyezetben való kezelésével kapcsolatos további információkért lásd: [virtuális gép készletének beállítása és kezelése](how-to-set-virtual-machine-passwords.md).

## <a name="next-steps"></a>Következő lépések
A tanterem Labs szolgáltatással kapcsolatos további tudnivalókért tekintse meg a cikkek [útmutatói](how-to-manage-lab-accounts.md)című cikket.
