---
title: Virtuális hálózat konfigurálása az Azure DevTest Labs ben | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhat egy meglévő virtuális hálózatot és alhálózatot, és hogyan használhatja őket virtuális gépben az Azure DevTest Labs alkalmazással
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 6cf3d2f82c98a3caab47ff48a600316747932b72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70390033"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Virtuális hálózat konfigurálása az Azure DevTest Labs ben
Acikk ben kifejtése virtuális [gép hozzáadása egy laborban,](devtest-lab-add-vm.md)amikor létrehoz egy virtuális gépet egy tesztkörnyezetben, megadhatja a konfigurált virtuális hálózat. Előfordulhat például, hogy a virtuális gépekről kell elérnie a testhálózati erőforrásokat az ExpressRoute-tal vagy a helyek közötti VPN-nel konfigurált virtuális hálózat használatával.

Ez a cikk bemutatja, hogyan adhat hozzá a meglévő virtuális hálózat ot a tesztkörnyezet virtuális hálózati beállításaihoz, hogy a virtuális gépek létrehozásakor választható legyen.

> [!NOTE]
> Az Azure Virtuális hálózat szolgáltatással kapcsolatos költségekről az [Azure Virtuális hálózat díjszabása](../virtual-network/virtual-networks-overview.md#pricing)című témakörben olvashat.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Virtuális hálózat konfigurálása egy tesztkörnyezethez az Azure Portal használatával
A következő lépések végigvezeti kontinami útján egy meglévő virtuális hálózat (és alhálózat) hozzáadása egy labor, hogy használható, amikor egy virtuális gép ugyanabban a tesztkörnyezetben. 

1. Jelentkezzen be az [Azure Portalra.](https://go.microsoft.com/fwlink/p/?LinkID=525040)
1. Válassza az **Összes szolgáltatás**lehetőséget, majd a listából válassza a **DevTest Labs** elemet.
1. A laborok listájából válassza ki a kívánt labort. 
1. A tesztkörnyezet fő ablaktábláján válassza a **Konfiguráció és házirendek**lehetőséget.

    ![A tesztkörnyezet konfigurációjának és házirendjeinek elérése](./media/devtest-lab-configure-vnet/policies-menu.png)
1. A **KÜLSŐ ERŐFORRÁSOK** CSOPORTban válassza a **Virtuális hálózatok**lehetőséget. Megjelenik az aktuális tesztkörnyezethez konfigurált virtuális hálózatok listája, valamint a tesztkörnyezethez létrehozott alapértelmezett virtuális hálózat. 
1. Válassza a **+ Hozzáadás** lehetőséget.
   
    ![Meglévő virtuális hálózat hozzáadása a laborhoz](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. A **Virtuális hálózat** ablaktáblán válassza a **[Virtuális hálózat kiválasztása]** lehetőséget.
   
    ![Meglévő virtuális hálózat kiválasztása](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. A **Válassza ki** a virtuális hálózat ablaktábláját, jelölje ki a kívánt virtuális hálózatot. Megjelenik egy lista, amely az összes olyan virtuális hálózatot megjeleníti, amely az előfizetésben ugyanabban a régióban van, mint a tesztkörnyezetben.
1. A virtuális hálózat kiválasztása után a rendszer visszakerül a **virtuális hálózat** ablaktáblájára. Jelölje ki az alhálózatot az alsó listában.

    ![Alhálózati lista](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Megjelenik a Lab alhálózat ablaktábla.

    ![Labor alhálózat ablaktábla](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Adja meg **a Lab alhálózat nevét.**
   - Ha engedélyezni szeretné egy alhálózat használatát a tesztkörnyezet virtuális gépének létrehozásában, jelölje be **a Használat a virtuális gép létrehozásához**lehetőséget.
   - A [megosztott nyilvános IP-cím](devtest-lab-shared-ip.md)engedélyezéséhez jelölje be **A megosztott nyilvános IP engedélyezése**lehetőséget.
   - Ha engedélyezni szeretné a nyilvános IP-címeket egy alhálózatban, jelölje be **a Nyilvános IP-létrehozás engedélyezése**jelölőnégyzetet.
   - A **Felhasználónkénti virtuális gépek maximális** száma mezőben adja meg az egyes alhálózatok felhasználónkénti maximális virtuális gépeit. Ha korlátlan számú virtuális gépet szeretne, hagyja üresen ezt a mezőt.
1. A Lab Subnet ablaktábla bezárásához válassza az **OK gombot.**
1. A Virtuális hálózat ablaktábla bezárásához válassza a **Mentés** lehetőséget.

Most, hogy a virtuális hálózat konfigurálva van, ki lehet választani, amikor virtuális gépet hoz létre. A virtuális gép létrehozásáról és a virtuális hálózat megadásáról a [Virtuális gép hozzáadása laborhoz](devtest-lab-add-vm.md)című cikkben olvashat. 

Az Azure [virtuális hálózati dokumentációja](https://docs.microsoft.com/azure/virtual-network) további információt nyújt a virtuális hálózatok használatáról, beleértve a virtuális hálózatok beállítását és kezelését, valamint a helyszíni hálózathoz való csatlakoztatását.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Miután hozzáadta a kívánt virtuális hálózatot a laborhoz, a következő lépés [egy virtuális gép hozzáadása a laborhoz.](devtest-lab-add-vm.md)

