---
title: Virtuális gép létrehozása egy statikus nyilvános IP-cím – Azure portal |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy virtuális gép statikus nyilvános IP-címet az Azure portal használatával.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: e9546bcc-f300-428f-b94a-056c5bd29035
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 524293f9a1ded73ee7cb6ba4f53208a9f9c54ffa
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38670984"
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Virtuális gép létrehozása egy statikus nyilvános IP-címet az Azure portal használatával

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [PowerShell (klasszikus)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk ismerteti a Microsoft azt javasolja, a klasszikus üzemi modell helyett új telepítések esetén a Resource Manager üzemi modell használatával.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Statikus nyilvános IP-Címmel rendelkező virtuális gép létrehozása

A virtuális gép létrehozása az Azure Portalon statikus nyilvános IP-címet, végezze el az alábbi lépéseket:

1. Egy böngészőből keresse fel az [Azure portált](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. A portál bal felső sarkában kattintson **erőforrás létrehozása**>>**számítási**>**Windows Server 2012 R2 Datacenter**.
3. Az a **telepítési modell kiválasztása** listájáról válassza ki a **Resource Manager** kattintson **létrehozás**.
4. Az a **alapjai** panelen adja meg a Virtuálisgép-adatok a következők szerint, és kattintson **OK**.
   
    ![Az Azure portal – alapvető tudnivalók](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. Az a **méret kiválasztása** ablaktáblán kattintson a **A1 Standard** , az alábbiak szerint, és kattintson **válassza**.
   
    ![Az Azure portal - méretének kiválasztása](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. Az a **beállítások** ablaktáblán kattintson a **nyilvános IP-cím**, ezt a a **nyilvános IP-cím létrehozása** panel alatt **hozzárendelés**, kattintson **Statikus** módon. És kattintson a **OK**.
   
    ![Az Azure Portal webhelyen – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. Az a **beállítások** ablaktáblán kattintson a **OK**.
8. Tekintse át a **összefoglalás** , az alábbiak szerint, és kattintson a panel **OK**.
   
    ![Az Azure Portal webhelyen – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Figyelje meg, hogy az új csempét az irányítópulton.
   
    ![Az Azure Portal webhelyen – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. A virtuális gép létrehozása után a **beállítások** ablaktábla megjeleníti az alábbiak szerint:
    
    ![Az Azure Portal webhelyen – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

## <a name="set-ip-addresses-within-the-operating-system"></a>Állítsa be az operációs rendszer belüli IP-címek

Meg kell soha nem hozzárendelheti manuálisan, a virtuális gép operációs rendszerén belül egy Azure virtuális géphez társított nyilvános IP-cím. Javasoljuk, hogy nem statikusan rendel a privát IP-cím az Azure virtuális gépen belül a virtuális gépek, az operációs rendszer rendelt, kivéve, ha szükséges, ha például [több IP-címek hozzárendelése virtuális géphez Windows](virtual-network-multiple-ip-addresses-portal.md). Ha manuálisan állítsa be a magánhálózati IP-címet az operációs rendszerből, érdekében, hogy az Azure-ban rendelt magánhálózati IP-cím megegyező címre [hálózati adapter](virtual-network-network-interface-addresses.md#change-ip-address-settings), vagy a virtuális gép is megszakad a kapcsolat. Tudjon meg többet [magánhálózati IP-cím](virtual-network-network-interface-addresses.md#private) beállításait.

## <a name="next-steps"></a>További lépések

Hálózati forgalmat, és a virtuális gépről a jelen cikkben létrehozott áramolhasson. Bejövő és kimenő biztonsági szabályok egy hálózati biztonsági csoporton belül, amely korlátozza a forgalmat, amely a hálózati adapter vagy az alhálózaton, illetve határozhatja meg. Hálózati biztonsági csoportokkal kapcsolatos további tudnivalókért lásd: [hálózati biztonsági csoportok áttekintése](security-overview.md).