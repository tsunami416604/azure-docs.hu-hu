---
title: "Virtuális hálózat konfigurálása a Azure DevTest Labs szolgáltatásban |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálja egy meglévő virtuális hálózat és az alhálózatot, és használhatja őket az Azure DevTest Labs szolgáltatásban virtuális gépen"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 6cda99c2-b87e-4047-90a0-5df10d8e9e14
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 19dbb1625f46f8864413dc538a96b2413bc6eea0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>A Azure DevTest Labs szolgáltatásban virtuális hálózat konfigurálása
A cikkben leírtak szerint [egy összetevőkkel rendelkező virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm-with-artifacts.md), egy tesztkörnyezetben, a virtuális gépek létrehozásakor megadhatja, hogy egy konfigurált virtuális hálózati. Mindezt egy például az is, ha a virtuális gépek a virtuális hálózaton keresztül, expressroute-on vagy a telephelyek közötti VPN konfigurálva lett a vállalati hálózat erőforrások eléréséhez szükséges. Az alábbi szakaszok bemutatják, hogyan lehet, hogy az elérhető, kiválaszthatja a virtuális gépek létrehozásakor adja hozzá a meglévő virtuális hálózatot a labor virtuális hálózati beállításait.

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Virtuális hálózat az Azure portál használatával labor beállítása
A következő lépések végigvezetik hozzáadása egy meglévő virtuális hálózat (és alhálózati) a laborkörnyezetben, hogy az azonos, amikor a virtuális gép létrehozásakor használható. 

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Válassza ki **több szolgáltatások**, majd válassza ki **DevTest Labs** a listából.
3. Válassza ki a kívánt labor labs listájának megtekintéséhez. 
4. A labor paneljén válassza **konfigurációs és házirendek**.
5. A tesztlabor a **konfigurációs és házirendek** panelen válassza **virtuális hálózatok**.
6. Az a **virtuális hálózatok** panelen, megjelenik az aktuális tesztkörnyezetben, valamint az alapértelmezett virtuális hálózat a tesztkörnyezethez létrehozott konfigurált virtuális hálózatokból álló listát. 
7. Válassza ki **+ Hozzáadás**.
   
    ![Meglévő virtuális hálózat hozzáadása a tesztkörnyezet](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
8. Az a **virtuális hálózati** panelen válassza **[Select virtuális hálózati]**.
   
    ![Válassza ki a meglévő virtuális hálózat](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
9. Az a **válasszon virtuális hálózati** panelen válassza ki a kívánt virtuális hálózatot. A panel látható a virtuális hálózatok alatti ugyanabban a régióban, mint a labor az előfizetéshez.  
10. Miután kiválasztott egy virtuális hálózatot, a rendszer visszairányítja a **virtuális hálózati** kattintson a panel alján a listában az alhálózatot.

    ![Alhálózati listája](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    A tesztkörnyezet alhálózati panel jelenik meg.

    ![Labor alhálózati panel](./media/devtest-lab-configure-vnet/lab-subnet.png)

11. Adjon meg egy **tesztkörnyezet alhálózatának nevét**.
12. Virtuális gép létrehozása, amikor használandó alhálózat engedélyezéséhez válassza **használja a virtuális gépek létrehozásához**.
13. Ahhoz, hogy egy [nyilvános IP-cím megosztott](devtest-lab-shared-ip.md), jelölje be **engedélyezése megosztott nyilvános IP-cím**.
14. Az alhálózat nyilvános IP-címek engedélyezéséhez válassza **nyilvános IP-létrehozásának engedélyezése**.
15. Az a **virtuális gépek maximális száma felhasználónként** mezőben adja meg a maximális virtuális gépek minden felhasználóhoz az egyes alhálózatokon. Ha azt szeretné, hogy a virtuális gépek korlátlan számú, hagyja üresen ezt a mezőt.
16. Válassza ki **OK** a labor alhálózati panel bezárásához.
17. Válassza ki **mentése** a virtuális hálózat panel bezárásához.
18. Most, hogy a virtuális hálózat van konfigurálva, akkor jelölhető ki a virtuális gépek létrehozásakor. 
    Hozzon létre egy virtuális Gépet, és adjon meg egy virtuális hálózatot, a cikkben találhat [egy összetevőkkel rendelkező virtuális gép hozzáadása egy laborhoz](devtest-lab-add-vm-with-artifacts.md). 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Következő lépések
Miután hozzáadta a kívánt virtuális hálózat a tesztkörnyezet, a következő lépés, hogy [a virtuális gépek hozzáadása a labor](devtest-lab-add-vm-with-artifacts.md).

