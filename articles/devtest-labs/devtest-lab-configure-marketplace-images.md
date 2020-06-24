---
title: Azure Marketplace-rendszerkép beállításainak konfigurálása Azure DevTest Labs
description: Annak konfigurálása, hogy mely Azure Marketplace-rendszerképek használhatók virtuális gépek létrehozásakor Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 804c6af2-17e9-4320-af3a-f454bd398379
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 2dc3aa000cefc0e65305b58f8fdce93b94bfd35f
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896304"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Azure Marketplace-rendszerkép beállításainak konfigurálása Azure DevTest Labs
A DevTest Labs támogatja a virtuális gépek Azure Marketplace-lemezképek alapján történő létrehozását, attól függően, hogy az Azure Marketplace-lemezképek hogyan legyenek felhasználva a laborban. Ez a cikk bemutatja, hogyan határozhatja meg, hogy mely, ha van ilyen, Azure Marketplace-lemezképeket használhat a virtuális gépek tesztkörnyezetben való létrehozásakor. Ez biztosítja, hogy a csapata csak a szükséges Piactéri rendszerképekhez férhet hozzá. 

## <a name="select-which-azure-marketplace-images-are-allowed-when-creating-a-vm"></a>Válassza ki, hogy mely Azure Marketplace-lemezképek engedélyezettek a virtuális gépek létrehozásakor
1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A laborok listájából válassza ki a kívánt labort. 
4. A labor paneljén válassza a **konfiguráció és szabályzatok**lehetőséget.
5. A tesztkörnyezet **konfigurációjának és házirendjeinek** panelén **válassza a** **Piactéri lemezképek**lehetőséget.
6. Itt adhatja meg, hogy az összes minősített Azure Marketplace-lemezkép elérhető legyen-e az új virtuális gép alapjaként való használatra. Ha az **Igen**lehetőséget választja, akkor az összes Azure Marketplace-rendszerkép, amely megfelel az alábbi feltételeknek, engedélyezett a laborban:
   
   * A rendszerkép egyetlen virtuális gépet hoz létre, **és**
   * A rendszerkép Azure Resource Managert használ a virtuális gépek kiépítéséhez, **és**
   * A rendszerkép nem igényel extra licencelési csomag megvásárlását
     
     Ha nem kívánja engedélyezni a lemezképek használatát, vagy szeretné megadni, hogy mely lemezképek használhatók, válassza a **nem**lehetőséget.
     
     ![Lehetőség arra, hogy az összes Piactéri lemezkép használható legyen a virtuális gépek alaprendszerképe](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Ha a **nem** lehetőséget választja az előző lépésben, az **engedélyezett lemezképek/az összes kijelölése** jelölőnégyzet engedélyezve van. 
   Ezt a lehetőséget a keresőmezőbe együtt használva gyorsan kiválaszthatja vagy kiválaszthatja a listában megjelenített elemeket.
   * Válassza ki azokat az Azure Marketplace-lemezképeket, amelyeket engedélyezni szeretne a virtuális gép létrehozásához az egyes lemezképek megfelelő jelölőnégyzetének ellenőrzésével.
   * Ha nem szeretné engedélyezni az Azure Marketplace-lemezképek használatát a laborban, válassza a Nothing elemet a listából.
   
     ![Megadhatja, hogy mely Azure Marketplace-lemezképek használhatók virtuális gépek alaprendszerképként való használatával](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Miután konfigurálta, hogy az Azure Marketplace-lemezképek hogyan engedélyezettek a virtuális gépek létrehozásakor, a következő lépés [egy virtuális gép hozzáadása a laborhoz](devtest-lab-add-vm.md).

