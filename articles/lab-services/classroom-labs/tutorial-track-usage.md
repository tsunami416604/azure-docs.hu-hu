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
ms.date: 07/23/2018
ms.author: spelluru
ms.openlocfilehash: 710d157dcf4c6d060e59bcfbb69455e2ddc91bdd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450130"
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
2. Győződjön meg róla, hogy látható a virtuális gépek állapota és az eddigi üzemórák mennyisége. Az Ön által a diákok virtuális gépein töltött idő nem számít az utolsó oszlopban megjelenített használati időbe. 

    ![Virtuálisgép-használat](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Diákok virtuális gépeinek felügyelete 
Ha az egér mutatóját valamelyik virtuális gép fölé viszi a listában, a következő műveletek vezérlőelemei jelennek meg: 

- Kapcsolódás virtuális géphez
- Virtuális gép elindítása
- Virtuális gép leállítása
- Virtuális gép törlése

![Virtuális gép vezérlői](../media/tutorial-track-usage/vm-controls.png) 



## <a name="next-steps"></a>További lépések
Az oktatóanyag bemutatta, hogyan tekintheti meg a tesztkörnyezetre regisztrált felhasználókat, miként követheti nyomon a tesztkörnyezetben lévő virtuális gépek használatát, valamint felügyelheti ezeket a virtuális gépeket.

Az osztálytermi tesztkörnyezetekkel kapcsolatos további információkért lásd [az útmutatók](how-to-manage-lab-accounts.md) megfelelő témaköreit.
