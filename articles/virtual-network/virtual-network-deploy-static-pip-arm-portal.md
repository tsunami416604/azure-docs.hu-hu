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
ms.openlocfilehash: 53e9b55a82e7750393dfb6c4818681028c5c4d2f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
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
2. Kattintson a portál bal felső sarkában **hozzon létre egy erőforrást**>>**számítási**>**Windows Server 2012 R2 Datacenter**.
3. Az a **telepítési modell kiválasztása** listára, válassza ki **erőforrás-kezelő** kattintson **létrehozása**.
4. Az a **alapjai** ablaktáblán, az alábbiak szerint adja meg a Virtuálisgép-adatok, és kattintson a **OK**.
   
    ![Azure portál – alapok](./media/virtual-network-deploy-static-pip-arm-portal/figure1.png)
5. A a **méret kiválasztása** ablaktáblán kattintson a **A1 szabványos** következő lehetőséget, majd kattintson **válasszon**.
   
    ![Azure portál – méret kiválasztása](./media/virtual-network-deploy-static-pip-arm-portal/figure2.png)
6. Az a **beállítások** ablaktáblán kattintson a **nyilvános IP-cím**, ezt a a **nyilvános IP-cím létrehozása** ablaktáblán, a **hozzárendelés**, kattintson **Statikus** a következőképpen. Majd, **OK**.
   
    ![Azure portál – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure3.png)
7. Az a **beállítások** ablaktáblán kattintson a **OK**.
8. Tekintse át a **összegzés** ablaktáblán, és kattintson **OK**.
   
    ![Azure portál – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure4.png)
9. Figyelje meg, hogy az új csempe az irányítópulton.
   
    ![Azure portál – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure5.png)
10. A virtuális gép létrehozása után a **beállítások** ablaktáblán megjelennek azok az alábbiak szerint:
    
    ![Azure portál – nyilvános IP-cím létrehozása](./media/virtual-network-deploy-static-pip-arm-portal/figure6.png)

