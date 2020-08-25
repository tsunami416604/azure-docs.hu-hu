---
title: Tesztkörnyezet használatának nyomon követése az Azure Lab Services szolgáltatásban | Microsoft Docs
description: Ebben az oktatóanyagban egy tesztkörnyezet létrehozójaként/tulajdonosaként követjük nyomon a tesztkörnyezet használatát.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: a9a9b49b568decc621be1969a8578d61ac7e4861
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "85445032"
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

    ![Regisztrált felhasználók](./media/tutorial-track-usage/registered-users.png)

    A labor felhasználók hozzáadásával és kezelésével kapcsolatos további információkért lásd: [labor-felhasználók hozzáadása és kezelése](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms"></a>Virtuális gépek használatának megtekintése

1. Válassza a **Virtuális gépek** lehetőséget a bal oldali menüben. 
2. Győződjön meg róla, hogy látható a virtuális gépek állapota és az eddigi üzemórák mennyisége. Az az idő, ameddig a labor tulajdonosa egy tanuló virtuális gépen elkölt, nem számít az utolsó oszlopban látható használati idő függvényében. 

    ![Virtuálisgép-használat](./media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Diákok virtuális gépeinek felügyelete 
Ezen az oldalon az **állapot** oszlopban vagy az eszköztáron található vezérlők használatával indíthatja el, állíthatja le vagy állíthatja alaphelyzetbe a tanulói virtuális gépeket.

![VIRTUÁLIS gépek műveletei](./media/tutorial-track-usage/vm-controls.png)

A virtuálisgép-készlet tesztkörnyezetben való kezelésével kapcsolatos további információkért lásd: [virtuális gépek készletének beállítása és kezelése](how-to-set-virtual-machine-passwords.md).

> [!NOTE]
> Ha egy oktató bekapcsol egy tanulói virtuális gépet, az nem érinti a tanulóra vonatkozó kvótát. A felhasználóhoz tartozó kvóta meghatározza, hogy a felhasználó számára hány labor óra legyen elérhető az ütemezett osztály időpontján kívül. A kvótákkal kapcsolatos további információkért lásd: [kvóták beállítása a felhasználók](how-to-configure-student-usage.md?#set-quotas-for-users)számára.

## <a name="next-steps"></a>További lépések
A tanterem Labs szolgáltatással kapcsolatos további tudnivalókért tekintse meg a cikkek [útmutatói](how-to-manage-lab-accounts.md)című cikket.
