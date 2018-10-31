---
title: Tesztkörnyezet elérése az Azure DevTest Labs szolgáltatásban | Microsoft Docs
description: Ebben az oktatóanyagban megtanul hozzáférni az Azure DevTest Labs szolgáltatással létrehozott tesztkörnyezethez, továbbá megtudhatja, hogyan foglalhatja le és használhatja a virtuális gépeket, illetve hogyan oldhatja fel a lefoglalást.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: ab52206230c4dfe2d92c97f1e291ee00a086c570
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470863"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Oktatóanyag: Tesztkörnyezet elérése az Azure DevTest Labs szolgáltatásban
Ebben az oktatóanyagban az [Oktatóanyag: Tesztkörnyezet létrehozása az Azure DevTest Labs szolgáltatásban](tutorial-create-custom-lab.md) című szakaszban létrehozott tesztkörnyezetet használatát ismerheti meg.

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Virtuális gép (VM) lefoglalása a tesztkörnyezetben
> * Kapcsolódás a virtuális géphez
> * A virtuális gép lefoglalásának feloldása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="access-the-lab"></a>Hozzáférés a tesztkörnyezethez

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki a bal oldali menü **Összes erőforrás** elemét. 
3. Válassza ki a **DevTest Labs** erőforrástípust. 
4. Válassza ki a tesztkörnyezetet. 

    ![A tesztkörnyezet kiválasztása](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Virtuális gép lefoglalása

1. A **Lefoglalható virtuális gépek** listában válassza a következőket: **...** (három pont), majd **Gép lefoglalása**.

    ![Virtuális gép lefoglalása](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Ellenőrizze, hogy a virtuális gép szerepel-e a **Saját virtuális gépek** listájában.

    ![Saját virtuális gép](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

1. Válassza ki a virtuális gépét a listából. Megjelenik a virtuális géphez tartozó **Virtuális gép oldal**. Válassza az eszköztár **Csatlakozás** elemét.

    ![Csatlakozás virtuális géphez](./media/tutorial-use-custom-lab/connect-button.png)
2. Mentse a letöltött **RDP**-fájlt a merevlemezre, majd a használatával csatlakozzon a virtuális géphez. Adja meg a felhasználónevet és jelszót, amelyeket a virtuális gép létrehozásakor határozott meg. 

    > [!NOTE] 
    > Linux rendszerű virtuális géphez való csatlakozáshoz a virtuális gép esetében engedélyezni kell az SSH- vagy az RDP-hozzáférést. A Linux rendszerű virtuális gépekhez történő RDP-csatlakozással kapcsolatban lásd: [Távoli asztal telepítése és konfigurálása Azure-beli Linux virtuális géphez való csatlakozáshoz](../virtual-machines/linux/use-remote-desktop.md). 


## <a name="unclaim-the-vm"></a>A virtuális gép lefoglalásának feloldása
Ha már nem használja a virtuális gépet, az alábbi lépéseket követve oldja fel azt: 

1. A virtuális gép oldalán válassza ki az eszköztár **Feloldás** elemét. 

    ![A virtuális gép lefoglalásának feloldása](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. A virtuális gép a lefoglalás feloldása előtt leáll. 

    ![Feloldás állapota](./media/tutorial-use-custom-lab/unclaim-status.png) 
1. Miután a feloldási művelet befejeződött, a virtuális gép megjelenik a **Lefoglalható virtuális gépek** lista alján. 
    
## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megismerkedett az Azure DevTest Labs használatával létrehozott tesztkörnyezet elérésének és használatának módjával. A tesztkörnyezetben lévő virtuális gépekhez való hozzáféréssel és azok használatával kapcsolatos további információkért tekintse meg a következőt: 

> [!div class="nextstepaction"]
> [Útmutató: Virtuális gépek használata tesztkörnyezetben](devtest-lab-add-vm.md)

