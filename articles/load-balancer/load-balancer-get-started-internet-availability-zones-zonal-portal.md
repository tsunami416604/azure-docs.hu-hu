---
title: Load Balancer létrehozása a zónaszintű előtér - az Azure Portalon
titlesuffix: Azure Load Balancer
description: Ismerje meg, hogyan hozhat létre egy Standard Load Balancer az Azure Portallal zónaszintű előtér
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: c81ff5ea330c4c0ba26a92a3b5399cfa961e4b2b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57856858"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Standard Load Balancer létrehozása az Azure portal használatával zónaszintű frontend

Ez a cikk végigvezeti egy nyilvános létrehozása [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) zónaszintű előtérbeli IP-konfigurációhoz. Hogyan működnek a rendelkezésre állási zónák a Standard Load Balancer ismertetése: [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md). 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> A rendelkezésre állási zónák támogatása az Azure-erőforrásokhoz, és a régiók és a virtuális gép méretcsaládjai érhető el. További információ az első lépésekről, és melyik Azure-erőforrások, régiók, és próbálja meg a rendelkezésre állási zónák a virtuális gép méretcsaládjai, lásd: [a rendelkezésre állási zónákat áttekintő](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>A zónaszintű előtérbeli IP-címmel rendelkező terheléselosztó létrehozása

1. Egy böngészőből keresse fel az Azure portal: [ https://portal.azure.com ](https://portal.azure.com) és az Azure-fiókjával jelentkezzen be.
2. A képernyő bal felső sarkában válassza **erőforrás létrehozása** > **hálózatkezelés** > **Load Balancert.**
3. Az a **terheléselosztó létrehozása** lap **neve** típus **myLoadBalancer**.
4. A **Típus** alatt válassza ki a **Nyilvános** elemet.
5. Válassza a Termékváltozat, **Standard**.
6. Kattintson a **nyilvános IP-cím kiválasztása**, kattintson a **új létrehozása**, majd a **nyilvános IP-cím létrehozása** oldalon, a név, típus **myPublicIPZonal**, Termékváltozat, válassza ki a **Standard**, válassza ki a rendelkezésre állási zónában, **1**.
    
>[!NOTE] 
> A nyilvános IP-cím ebben a lépésben létrehozott része Standard termékváltozat alapértelmezés szerint.

1. A **erőforráscsoport**, kattintson a **új létrehozása**, majd írja be **myResourceGroupZLB** az erőforráscsoport neveként.
1. A **hely**válassza **Nyugat-Európa**, és kattintson a **OK**. Ekkor elindul a terheléselosztó üzembe helyezése, ami néhány perc alatt sikeresen befejeződik.

    ![zónaredundáns Standard Load Balancer létrehozása az Azure portal használatával](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).



