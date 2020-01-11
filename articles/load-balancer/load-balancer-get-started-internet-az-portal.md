---
title: Load Balancer létrehozása a Zone-redundáns frontend-Azure Portal
titleSuffix: Azure Load Balancer
description: Megtudhatja, hogyan hozhat létre nyilvános standard Load Balancer a zóna-redundáns nyilvános IP-cím felülettel a Azure Portal
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: allensu
ms.openlocfilehash: bd85e062b2b45abc32269b94ce678ebf63fb3fc1
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894450"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>standard Load Balancer létrehozása a redundáns előtérrel Azure Portal használatával

Ez a cikk a nyilvános IP-címet használó, nyilvános IP-címmel rendelkező, nyilvános [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) létrehozásához szükséges lépéseket ismerteti. Egy standard Load Balancer előtér-IP-címe alapértelmezés szerint a zóna-redundáns.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
>  A Availability Zones támogatása elérhető az Azure-erőforrások és-régiók kiválasztásához, valamint a virtuális gépek méretének megadásához. További információ az első lépésekről, valamint arról, hogy az Azure-erőforrások,-régiók és a virtuális gépek méretének családja milyen módon próbálhatja ki a rendelkezésre állási zónákat: [Availability Zones áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-portal/supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-zone-redundant-load-balancer"></a>Zóna redundáns Load Balancer létrehozása

1. Egy böngészőből navigáljon a Azure Portal: [https://portal.azure.com](https://portal.azure.com) , és jelentkezzen be az Azure-fiókjával.
2. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **Load Balancer lehetőséget.**
3. A **Load Balancer létrehozása** lapon a **név** mezőbe írja be a következőt: **myLoadBalancer**.
4. A **Típus** alatt válassza ki a **Nyilvános** elemet.
5. Az SKU területen válassza a **standard**lehetőséget.
6. Kattintson a **nyilvános IP-cím**, majd az **új létrehozása**elemre, és a **nyilvános IP-cím létrehozása** lapon a név mezőbe írja be a következőt: **myPublicIPStandard**.
    >[!NOTE] 
    > Az ebben a lépésben létrehozott nyilvános IP-cím szabványos SKU, és alapértelmezés szerint zóna-redundáns. 
8. A **hely**területen válassza a **keleti régiója**lehetőséget, majd kattintson **az OK**gombra. Ekkor elindul a terheléselosztó üzembe helyezése, ami néhány perc alatt sikeresen befejeződik.

## <a name="next-steps"></a>Következő lépések
- További információ a [standard Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md).



