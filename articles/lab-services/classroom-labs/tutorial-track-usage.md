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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 315ebfa1460f9d9bc041925cec2451f63ac5be16
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580233"
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
4. A bal oldali menüben vagy a **felhasználók** csempén válassza a **felhasználók** lehetőséget. Megjelenik a tesztkörnyezetben regisztrált felhasználók listája. Válassza a **Regisztrációs hivatkozás** lehetőséget, másolja a hivatkozást a vágólapra, és küldje el minden új tanulónak, aki még nem regisztrált a tesztkörnyezetbe. 

    ![Regisztrált felhasználók](../media/tutorial-track-usage/registered-users.png)

    A tesztkörnyezet felhasználói hozzáadásával és kezelésével kapcsolatos további információkért lásd: [labor-felhasználók hozzáadása és kezelése](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms-in-the-lab"></a>A tesztkörnyezetben lévő virtuális gépek használati adatainak megtekintése 

1. Válassza a **Virtuális gépek** lehetőséget a bal oldali menüben. 
2. Győződjön meg róla, hogy látható a virtuális gépek állapota és az eddigi üzemórák mennyisége. Az az idő, ameddig a labor tulajdonosa egy tanuló virtuális gépen elkölt, nem számít az utolsó oszlopban látható használati idő függvényében. 

    ![Virtuálisgép-használat](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Diákok virtuális gépeinek felügyelete 
Ezen az oldalon az **állapot** oszlopban vagy az eszköztáron található vezérlők használatával indíthatja el, állíthatja le vagy állíthatja alaphelyzetbe a tanulói virtuális gépeket.

![Virtuális gép vezérlői](../media/tutorial-track-usage/vm-controls.png)

A virtuálisgép-készlet tesztkörnyezetben való kezelésével kapcsolatos további információkért lásd: [virtuális gép készletének beállítása és kezelése](how-to-set-virtual-machine-passwords.md).

## <a name="next-steps"></a>További lépések
A tanterem Labs szolgáltatással kapcsolatos további tudnivalókért tekintse meg a cikkek [útmutatói](how-to-manage-lab-accounts.md)című cikket.
