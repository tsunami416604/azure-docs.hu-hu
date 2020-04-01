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
ms.openlocfilehash: a1a3e62646fedd468a02eac7b1a48d0b2d00fd74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77591982"
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
4. Válassza a Bal oldali menü Felhasználók vagy **Felhasználók** **csempéjét.** Megjelenik a tesztkörnyezetben regisztrált felhasználók listája.  

    ![Regisztrált felhasználók](../media/tutorial-track-usage/registered-users.png)

    A tesztkörnyezet felhasználóinak hozzáadásáról és kezeléséről a [Tesztkörnyezet-felhasználók hozzáadása és kezelése](how-to-configure-student-usage.md)című témakörben talál további információt.

## <a name="view-the-usage-of-vms"></a>Virtuális gépek használatának megtekintése

1. Válassza a **Virtuális gépek** lehetőséget a bal oldali menüben. 
2. Győződjön meg róla, hogy látható a virtuális gépek állapota és az eddigi üzemórák mennyisége. Az az idő, amelyet a tesztkörnyezet tulajdonosa tölt egy diák virtuális gép nem számít bele az utolsó oszlopban látható használati idő. 

    ![Virtuálisgép-használat](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Diákok virtuális gépeinek felügyelete 
Ezen a lapon az **Állapot** oszlopban vagy az eszköztáron lévő vezérlők használatával elindíthatja, leállíthatja vagy alaphelyzetbe állíthatja a tanuló virtuális gépeket.

![Virtuálisgép-műveletek](../media/tutorial-track-usage/vm-controls.png)

A tesztkörnyezet virtuálisgép-készletének kezeléséről a [Virtuálisgép-készlet beállítása és kezelése](how-to-set-virtual-machine-passwords.md)című témakörben talál további információt.

> [!NOTE]
> Amikor egy oktató bekapcsolja a tanuló virtuális gép, kvóta a hallgató nem érinti. A felhasználó kvótája megadja, hogy az ütemezett óraidőn kívül hány laboróra érhető el a felhasználó számára. A kvótákról a [Kvóták beállítása felhasználókszámára című](how-to-configure-student-usage.md?#set-quotas-for-users)témakörben talál további információt.

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni az osztálytermi laborokról, olvassa el [az Útmutató útmutatók](how-to-manage-lab-accounts.md)című témakörben.
