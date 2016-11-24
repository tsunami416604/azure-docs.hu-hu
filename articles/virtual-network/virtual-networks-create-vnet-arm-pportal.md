---
title: "Virtuális hálózat létrehozása az Azure Portallal | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre virtuális hálózatot az Azure Portal használatával | Resource Manager."
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 6e96471c4f61e1ebe15c23f87ac646001d8e30ee
ms.openlocfilehash: d68f6a7e935f530630ee33f48cfad1b9e01e66a8


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Virtuális hálózat létrehozása az Azure Portallal

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Az Azure két üzemi modellel rendelkezik, az Azure Resource Managerrel és a klasszikussal. A Microsoft azt javasolja, hogy az erőforrások létrehozásához használja a Resource Manager-alapú üzemi modellt. A két modell közti különbségekkel kapcsolatos további információkért olvassa el [Az Azure üzemi modelljeinek megismerése](../resource-manager-deployment-model.md) című cikket.
 
Ez a cikk elmagyarázza, hogyan hozhat létre egy VNetet a Resource Manager-alapú üzemi modellben az Azure Portallal. Resource Manager-alapú VNetet létrehozhat egyéb eszközökkel is, illetve létrehozhat VNetet a klasszikus üzemi modellben is, ha az alábbi listából egy másik lehetőséget választ:

> [!div class="op_single_selector"]
- [Portál](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [Parancssori felület](virtual-networks-create-vnet-arm-cli.md)
- [Sablon](virtual-networks-create-vnet-arm-template-click.md)
- [Portál (klasszikus)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (klasszikus)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Parancssori felület (klasszikus)](virtual-networks-create-vnet-classic-cli.md)


[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

A virtuális hálózat Azure Portalon való létrehozásához végezze el az alábbi lépéseket:

1. Egy böngészőből keresse fel a http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson az **Új** > **Hálózat** > **Virtuális hálózat** elemre, amint a következő képen látható:

    ![Új virtuális hálózat](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. A megjelenő **Virtuális hálózat** panelen ellenőrizze, hogy a *Resource Manager* van-e kiválasztva, majd kattintson a **Létrehozás** gombra, amint a következő képen látható:

    ![Virtual Network](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. A megjelenő **Virtuális hálózat létrehozása** panelen írja be a *TestVNet* nevet a **Név**, a *192.168.0.0/16* címet a **Címtér**, a *FrontEnd* nevet az **Alhálózat neve**, a *192.168.1.0/24* címet az **Alhálózati címtartomány** és a *TestRG* nevet az **Erőforráscsoport** mezőbe, válassza ki az **Előfizetését** és a **Helyet**, majd kattintson a **Létrehozás** gombra, amint a következő képen látható:

    ![Virtuális hálózat létrehozása](./media/virtual-network-create-vnet-arm-pportal/3.png)

    Másik lehetőségként választhat egy meglévő erőforráscsoportot is. Az erőforráscsoportokkal kapcsolatos további információkért olvassa el [A Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md#resource-groups) című cikket. Választhat másik helyet is. Az Azure-helyekkel és -régiókkal kapcsolatos további információkért olvassa el az [Azure-régiók](https://azure.microsoft.com/regions) című cikket.

5. A portál VNet létrehozása esetén csak egyetlen alhálózat létrehozását engedi. Ebben a forgatókönyvben egy második alhálózatot is létre kell hozni a VNet létrehozását követően. A második alhálózat létrehozásához kattintson a **Minden erőforrás** gombra, majd a **TestVNet** elemre a **Minden erőforrás** panelen, amint a következő képen látható:

    ![VNet létrehozva](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. A megjelenő **TestVNet** panelen kattintson az **Alhálózat**, majd a **+Alhálózat** gombra, írja be a *BackEnd* nevet a **Név** és a *192.168.2.0/24* címet a **Címtartomány** mezőbe az **Alhálózat hozzáadása** panelen, majd kattintson az **OK** gombra, amint a következő képen látható:

    ![Alhálózat hozzáadása](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. A két alhálózat szerepel a listában, amint a következő képen látható:
    
    ![A VNet alhálózatainak listája](./media/virtual-network-create-vnet-arm-pportal/6.png)

Ez a cikk a két alhálózattal rendelkező virtuális hálózat tesztelési célokra való létrehozását magyarázta el. Mielőtt létrehozna üzemi használatra egy virtuális hálózatot, javasoljuk, hogy olvassa el a [Virtuális hálózatok áttekintése](virtual-networks-overview.md) és a [Virtuális hálózatok tervezése és kialakítása](virtual-network-vnet-plan-design-arm.md) című cikkeket, hogy teljes mértékben átlássa a virtuális hálózatokat és azok beállításait. 

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan csatlakoztathat:

- virtuális gépeket (VM) virtuális hálózatokhoz – olvassa el a [Windows virtuális gép létrehozását](../virtual-machines/virtual-machines-windows-hero-tutorial.md) vagy a [Linux virtuális gép létrehozását](../virtual-machines/virtual-machines-linux-quick-create-portal.md) ismertető cikkeket. Ha nem szeretne egy VNetet és alhálózatot létrehozni a cikkben ismertetett lépések szerint, használhat meglévő VNetet és alhálózatot, amelyekhez kapcsolódhat.
- virtuális hálózatot más virtuális hálózatokhoz – olvassa el a [virtuális hálózatok csatlakoztatását](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) ismertető cikket.
- virtuális hálózatot helyszíni hálózathoz helyek közti virtuális magánhálózat (VPN) vagy ExpressRoute-kapcsolatcsoport használatával. Az elsajátításához olvassa el a [Virtuális hálózat csatlakoztatása helyszíni hálózathoz helyek közti VPN használatával](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) és a [Virtuális hálózat csatlakoztatása ExpressRoute-kapcsolatcsoporthoz](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md) eljárásokat ismertető cikkeket.


<!--HONumber=Nov16_HO3-->


