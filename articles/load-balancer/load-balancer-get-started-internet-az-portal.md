---
title: Load Balancer létrehozása a Zone-redundáns frontend-Azure Portal
titlesuffix: Azure Load Balancer
description: Ismerje meg, hogyan hozhat létre egy nyilvános Standard Load Balancer zónaredundáns nyilvános előtérbeli IP-Címmel cím az Azure portal használatával
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: allensu
ms.openlocfilehash: a875fc3446944b8a39f43b29440ab1d7179b847d
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275082"
---
#  <a name="create-a-standard-load-balancer-with-zone-redundant-frontend-using-azure-portal"></a>Standard Load Balancer létrehozása az Azure portal használatával, a zónaredundáns frontend

Ez a cikk a nyilvános IP-címet használó, nyilvános IP-címmel rendelkező, nyilvános [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) létrehozásához szükséges lépéseket ismerteti. A Standard Load Balancer egy egyetlen előtérbeli IP-cím zónaredundáns alapértelmezés szerint.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
>  A rendelkezésre állási zónák támogatása az Azure-erőforrásokhoz, és a régiók és a virtuális gép méretcsaládjai érhető el. További információ az első lépésekről, és melyik Azure-erőforrások, régiók, és próbálja meg a rendelkezésre állási zónák a virtuális gép méretcsaládjai, lásd: [a rendelkezésre állási zónákat áttekintő](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-zone-redundant-load-balancer"></a>Zóna zónaredundáns load balancer létrehozása

1. Egy böngészőből keresse fel az Azure portal: [ https://portal.azure.com ](https://portal.azure.com) és az Azure-fiókjával jelentkezzen be.
2. A képernyő bal felső sarkában válassza **erőforrás létrehozása** > **hálózatkezelés** > **Load Balancert.**
3. Az a **terheléselosztó létrehozása** lap **neve** típus **myLoadBalancer**.
4. A **Típus** alatt válassza ki a **Nyilvános** elemet.
5. Válassza a Termékváltozat, **Standard**.
6. Kattintson a **nyilvános IP-cím**, kattintson a **új létrehozása**, majd a **nyilvános IP-cím létrehozása** oldalon, a név, típus **myPublicIPStandard**.
    >[!NOTE] 
    > A nyilvános IP-ebben a lépésben létrehozott Standard termékváltozat és zónaredundáns alapértelmezés szerint. 
8. Alatt **hely**válassza **USA 2. keleti régiója**, és kattintson a **OK**. Ekkor elindul a terheléselosztó üzembe helyezése, ami néhány perc alatt sikeresen befejeződik.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Standard Load Balancer és rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).



