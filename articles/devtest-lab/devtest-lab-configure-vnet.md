---
title: "Virtuális hálózat konfigurálása a Azure DevTest Labs szolgáltatásban |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálja egy meglévő virtuális hálózat és az alhálózatot, és használhatja őket az Azure DevTest Labs szolgáltatásban virtuális gépen"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: v-craic
ms.openlocfilehash: 037c3dd1ae3cc77ee6a0224149f3b099b3af8641
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>A Azure DevTest Labs szolgáltatásban virtuális hálózat konfigurálása
A cikkben leírtak szerint [virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm.md), egy tesztkörnyezetben, a virtuális gépek létrehozásakor megadhatja, hogy egy konfigurált virtuális hálózati. Szükség lehet például a virtuális gépek a virtuális hálózaton keresztül, expressroute-on vagy a telephelyek közötti VPN konfigurálva lett a vállalati hálózat erőforrások eléréséhez.

Ez a cikk azt ismerteti, hogyan számára, hogy hozzáadják a meglévő virtuális hálózatot a labor virtuális hálózati beállításait, hogy válassza ki a virtuális gépek létrehozásakor a rendelkezésére áll.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Virtuális hálózat az Azure portál használatával labor beállítása
A következő lépések végigvezetik hozzáadása egy meglévő virtuális hálózat (és alhálózati) a laborkörnyezetben, hogy az azonos, amikor a virtuális gép létrehozásakor használható. 

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Válassza ki a kívánt labor labs listájának megtekintéséhez. 
1. A labor főpanelen válassza **konfigurációs és házirendek**.

    ![A tesztkörnyezet konfigurációs és házirendek](./media/devtest-lab-configure-vnet/policies-menu.png)
1. Az a **külső erőforrások** szakaszban jelölje be **virtuális hálózatok**. Az aktuális tesztkörnyezetben konfigurált virtuális hálózatok listája jelenik meg, továbbá az alapértelmezett a tesztkörnyezethez létrehozott virtuális hálózatban. 
1. Válassza ki **+ Hozzáadás**.
   
    ![Meglévő virtuális hálózat hozzáadása a tesztkörnyezet](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Az a **virtuális hálózati** ablaktáblán válassza előbb **[Select virtuális hálózati]**.
   
    ![Válassza ki a meglévő virtuális hálózat](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Az a **válasszon virtuális hálózati** ablaktáblán válassza ki a kívánt virtuális hálózatot. A listán jelenik meg, melyen az összes ugyanabban a régióban, mint a labor az előfizetéshez a virtuális hálózaton.
1. Miután kiválasztott egy virtuális hálózatot, a rendszer visszairányítja a **virtuális hálózati** ablaktáblán. A lap alján a listában jelölje ki az alhálózatot.

    ![Alhálózati listája](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    A tesztkörnyezet alhálózati ablaktáblán jelenik meg.

    ![Labor alhálózati ablaktábla](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Adjon meg egy **tesztkörnyezet alhálózatának nevét**.
   - Virtuális gép létrehozása, amikor használandó alhálózat engedélyezéséhez válassza **használja a virtuális gépek létrehozásához**.
   - Ahhoz, hogy egy [nyilvános IP-cím megosztott](devtest-lab-shared-ip.md), jelölje be **engedélyezése megosztott nyilvános IP-cím**.
   - Az alhálózat nyilvános IP-címek engedélyezéséhez válassza **nyilvános IP-létrehozásának engedélyezése**.
   - Az a **virtuális gépek maximális száma felhasználónként** mezőben adja meg a maximális virtuális gépek minden felhasználóhoz az egyes alhálózatokon. Ha azt szeretné, hogy a virtuális gépek korlátlan számú, hagyja üresen ezt a mezőt.
1. Válassza ki **OK** a labor alhálózati ablak bezárásához.
1. Válassza ki **mentése** a virtuális hálózati ablak bezárásához.

Most, hogy a virtuális hálózat van konfigurálva, akkor jelölhető ki a virtuális gépek létrehozásakor. Hozzon létre egy virtuális Gépet, és adjon meg egy virtuális hálózatot, a cikkben találhat [virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm.md). 

Azure [virtuális hálózati dokumentáció](https://docs.microsoft.com/azure/virtual-network) Vnetek, többek között beállítása és kezelése egy Vnetet, és csatlakoztassa a helyszíni hálózat használatával kapcsolatos további információk.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Miután hozzáadta a kívánt virtuális hálózat a tesztkörnyezet, a következő lépés, hogy [a virtuális gépek hozzáadása a labor](devtest-lab-add-vm.md).

