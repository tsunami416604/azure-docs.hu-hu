---
title: Hozzáférés egyéni tesztkörnyezethez az Azure DevTest Labs szolgáltatásban | Microsoft Docs
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
ms.date: 04/09/2018
ms.author: spelluru
ms.openlocfilehash: ce4522673bac56f73944413d102b7cb36cf93f30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-access-a-custom-lab-in-azure-devtest-labs"></a>Oktatóanyag: Hozzáférés egy egyéni tesztkörnyezethez az Azure DevTest Labs szolgáltatásban
Ebben az oktatóanyagban az [Oktatóanyag: Egyéni tesztkörnyezet létrehozása](tutorial-create-custom-lab.md) című szakaszban létrehozott egyéni tesztkörnyezetet használatát ismerheti meg.

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Virtuális gép (VM) lefoglalása az egyéni tesztkörnyezetben
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

## <a name="unclaim-the-vm"></a>A virtuális gép lefoglalásának feloldása
Ha már nem használja a virtuális gépet, az alábbi lépéseket követve oldja fel azt: 

1. A virtuális gép oldalán válassza ki az eszköztár **Feloldás** elemét. 

    ![A virtuális gép lefoglalásának feloldása](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. A virtuális gép a lefoglalás feloldása előtt leáll. 

    ![Feloldás állapota](./media/tutorial-use-custom-lab/unclaim-status.png) 
1. Miután a feloldási művelet befejeződött, a virtuális gép megjelenik a **Lefoglalható virtuális gépek** lista alján. 
    
## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megismerkedett az Azure DevTest Labs használatával létrehozott egyéni tesztkörnyezet elérésének és használatának módjával. Az egyéni tesztkörnyezetben lévő virtuális gépekhez való hozzáféréssel és azok használatával kapcsolatos további információért tekintse meg a következőt: 

> [!div class="nextstepaction"]
> [Útmutató: Virtuális gépek használata egyéni tesztkörnyezetben](devtest-lab-add-vm.md)

