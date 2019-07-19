---
title: Load Balancer létrehozása egy zóna-előtérbeli felülettel – Azure Portal
titlesuffix: Azure Load Balancer
description: Megtudhatja, hogyan hozhat létre standard Load Balancer a zóna-előtérben a Azure Portal
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: allensu
ms.openlocfilehash: 9a260c267febb3dc851fe0a8ca8a8e351124116b
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275139"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>standard Load Balancer létrehozása a zóna-előtérben a Azure Portal használatával

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre egy nyilvános [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) egy zóna-előtérbeli IP-konfigurációval. Hogyan működnek a rendelkezésre állási zónák a Standard Load Balancer ismertetése: [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md). 

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

    ![zóna létrehozása – redundáns standard Load Balancer a Azure Portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).



