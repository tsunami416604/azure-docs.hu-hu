---
title: "Hozzon létre egy virtuális Gépet egy statikus nyilvános IP-cím - Azure-portálon |} Microsoft Docs"
description: "Útmutató: virtuális gép létrehozása az Azure portál használatával statikus nyilvános IP-cím."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
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
ms.openlocfilehash: 233e4eea8439320c1c7446e2c2b2e9d379351a3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-vm-with-a-static-public-ip-address-using-the-azure-portal"></a>Hozzon létre egy virtuális Gépet egy statikus nyilvános IP-cím az Azure portál használatával

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Azure CLI](virtual-network-deploy-static-pip-arm-cli.md)
> * [Sablon](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (klasszikus)](virtual-networks-reserved-public-ip.md)

[!INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a Microsoft azt javasolja, hogy a klasszikus üzembe helyezési modellel helyett az új telepítések esetén a Resource Manager telepítési modell használatát bemutatja.

[!INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## <a name="create-a-vm-with-a-static-public-ip"></a>Hozzon létre egy virtuális Gépet egy statikus nyilvános IP-cím

Virtuális gép létrehozása az Azure portálon statikus nyilvános IP-cím, kövesse az alábbi lépéseket:

1. Egy böngészőből keresse fel az [Azure portált](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. A portál felső bal oldali sarokban, kattintson a **új**>>**számítási**>**Windows Server 2012 R2 Datacenter**.
3. Az a **telepítési modell kiválasztása** listára, válassza ki **erőforrás-kezelő** kattintson **létrehozása**.
4. Az a **alapjai** panelen adja meg a Virtuálisgép-adatok alább látható módon, és kattintson a **OK**.
   
    ![Azure portál – alapok](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. A a **méret kiválasztása** panelen kattintson a **A1 szabványos** az alábbi módon, majd **kiválasztása**.
   
    ![Azure portál – méret kiválasztása](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. Az a **beállítások** panelen kattintson **nyilvános IP-cím**, ezt a a **nyilvános IP-cím létrehozása** panelen, a **hozzárendelés**, kattintson a **statikus** alább látható módon. Majd **OK**.
   
    ![Azure portál – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. Az a **beállítások** panelen kattintson a **OK**.
8. Tekintse át a **összegzés** panelen, a lent látható módon, és kattintson **OK**.
   
    ![Azure portál – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Figyelje meg, hogy az új csempe az irányítópulton.
   
    ![Azure portál – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. A virtuális gép létrehozása után a **beállítások** panelen megjelenik a lent látható módon
    
    ![Azure portál – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

