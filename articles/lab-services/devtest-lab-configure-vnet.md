---
title: Virtuális hálózat konfigurálása az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja egy meglévő virtuális hálózatot és alhálózatot, és használja őket egy virtuális Gépet az Azure DevTest Labs szolgáltatásban
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
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 8fb3b4ac748fcae2e3aad5b3bfb2a893340dc61a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256136"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Virtuális hálózat konfigurálása az Azure DevTest Labs szolgáltatásban
A cikkben leírtak szerint [virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm.md), amikor egy virtuális Gépet hoz létre a tesztkörnyezetben, megadhat egy konfigurált virtuális hálózatot. Szüksége lehet például a virtuális gépek a virtuális hálózat az ExpressRoute- vagy site-to-site VPN használatára konfigurált használata a vállalati hálózaton kívüli erőforrások eléréséhez.

Ez a cikk bemutatja, hogyan adhat hozzá egy tesztkörnyezet egy virtuális hálózati beállításait a meglévő virtuális hálózatot, hogy válassza ki a virtuális gépek létrehozásakor a rendelkezésére áll.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Az Azure portal használatával labor a virtuális hálózat konfigurálása
A következő lépések végigvezetik hozzáadása egy meglévő virtuális hálózat (és alhálózati) laborba, hogy ugyanazt a lab-ben a virtuális gép létrehozásakor használható. 

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **minden szolgáltatás**, majd válassza ki **DevTest Labs** a listából.
1. Tesztkörnyezetek listájában jelölje ki a kívánt tesztkörnyezetben. 
1. Válassza ki a fő panelen a labor **Konfigurace a zásady**.

    ![A labor konfigurace a zásady eléréséhez](./media/devtest-lab-configure-vnet/policies-menu.png)
1. Az a **külső erőforrások** szakaszban jelölje be **virtuális hálózatok**. A tesztkörnyezethez létrehozott alapértelmezett virtuális hálózatot és a jelenlegi labor konfigurált virtuális hálózatok listája jelenik meg. 
1. Válassza a **+ Hozzáadás** lehetőséget.
   
    ![Meglévő virtuális hálózat hozzáadása a tesztkörnyezethez](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. Az a **virtuális hálózati** ablaktáblán válassza **[válassza ki virtuális hálózatot]**.
   
    ![Válassza ki a meglévő virtuális hálózaton](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. Az a **válasszon egy virtuális hálózatot** ablaktáblán válassza ki a kívánt virtuális hálózatot. Egy listában látható a virtuális hálózatok ugyanabban a régióban a lab mint az előfizetés alatt álló összes.
1. Egy virtuális hálózat kiválasztása után a rendszer visszairányítja az **virtuális hálózati** ablaktáblán. Az alsó részén található listában válassza ki az alhálózatot.

    ![Alhálózatok listájának](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    A Lab alhálózati panelen jelenik meg.

    ![Labor alhálózati panel](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Adjon meg egy **labor alhálózatnév**.
   - Válassza ki ahhoz, hogy a virtuális gép létrehozása a lab-ben használt alhálózat, **használja a virtuális gép létrehozásakor**.
   - Ahhoz, hogy egy [megosztott nyilvános IP-cím](devtest-lab-shared-ip.md)válassza **engedélyezése megosztott nyilvános IP-cím**.
   - Válassza ki ahhoz, hogy a nyilvános IP-cím az alhálózat, **nyilvános IP-létrehozás engedélyezése**.
   - Az a **maximális virtuális gépek felhasználónként** mezőben adja meg a maximális virtuális gépeket az egyes alhálózatokon felhasználónként. Ha azt szeretné, hogy a virtuális gépek korlátlan számú, hagyja üresen ezt a mezőt.
1. Válassza ki **OK** a labor alhálózati panel bezárásához.
1. Válassza ki **mentése** gombra kattintva zárja be a virtuális hálózat panelen.

Most, hogy a virtuális hálózat van konfigurálva, a virtuális gép létrehozásakor kiválasztható. Megtudhatja, hogyan hozzon létre egy virtuális Gépet, és adjon meg egy virtuális hálózatot, a cikkben [virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm.md). 

Az Azure [Virtual Network-dokumentáció](https://docs.microsoft.com/azure/virtual-network) virtuális hálózatokhoz, például hogyan állíthatja be és felügyelheti a Vneteket, és csatlakoztassa a helyszíni hálózathoz való használatával kapcsolatos további információkat biztosít.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>További lépések
Miután hozzáadta a kívánt virtuális hálózatot a tesztkörnyezet, a következő lépés az, hogy [virtuális gép hozzáadása a labor](devtest-lab-add-vm.md).

