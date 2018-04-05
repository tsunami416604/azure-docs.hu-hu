---
title: Hozzon létre egy Load Balancer szabványos nyilvános zonal nyilvános IP-cím cím előtér Azure-portál használatával |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Load Balancer szabványos nyilvános zonal nyilvános IP-cím cím előtér és az Azure portál
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: 533c48b3a004f85dfbd2970d73dcf7de21811dca
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-public-ip-address-frontend-using-azure-portal"></a>Hozzon létre egy Load Balancer szabványos nyilvános zonal nyilvános IP-cím cím előtér Azure-portál használatával

Ez a cikk lépésről-lépésre nyilvános létrehozása [Load Balancer szabványos](https://aka.ms/azureloadbalancerstandard) rendelkező zonal időtúllépést. Ha szeretné megtudni, hogyan működnek a rendelkezésre állási zónák a szabványos terheléselosztóhoz, lásd: [szabványos terheléselosztó és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md). 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> Rendelkezésre állási zónák támogatása select Azure-erőforrások és a régiók és a virtuális gép mérete családok érhető el. További információ az első lépések, és mely Azure-erőforrások, régiók és virtuális gép mérete családok megpróbálhatja a rendelkezésre állási zónákat, lásd: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Hozzon létre egy terhelés-kiegyenlítő zonal előtérbeli IP-cím

1. Egy böngészőből keresse meg az Azure-portálon: [ http://portal.azure.com ](http://portal.azure.com) és az Azure-fiókkal történő bejelentkezés.
2. A képernyő bal felső oldalán válassza ki **hozzon létre egy erőforrást** > **hálózati** > **terheléselosztóhoz.**
3. Az a **létrehozás terheléselosztó** lap **neve** típus **myLoadBalancer**.
4. A **Típus** alatt válassza ki a **Nyilvános** elemet.
5. A Termékváltozat, válassza a **szabványos**.
6. Kattintson a **egy nyilvános IP-cím kiválasztása**, kattintson a **hozzon létre új**, majd a **nyilvános IP-cím létrehozása** lapon a nevét, típusát **myPublicIPZonal**, Termékváltozat, válassza ki a **szabványos**, rendelkezésre állási zóna, válassza ki a **1**.
    
>[!NOTE] 
> A nyilvános IP-cím ebben a lépésben létrehozott része a Standard Termékváltozat alapértelmezés szerint.

7. A **erőforráscsoport**, kattintson a **hozzon létre új**, majd írja be **myResourceGroupZLB** az erőforráscsoport neve.
8. A **hely**, jelölje be **Nyugat-Európában**, és kattintson a **OK**. Ekkor elindul a terheléselosztó üzembe helyezése, ami néhány perc alatt sikeresen befejeződik.

    ![zónaredundáns Load Balancer szabványos létrehozása az Azure-portálon](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>További lépések
- További információ [szabványos terheléselosztó és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).



