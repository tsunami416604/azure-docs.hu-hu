---
title: Hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér Azure-portál használatával |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér és az Azure portál
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
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 9a51638ea6d85178e6631ac278c116e4c7e05d61
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér Azure-portál használatával

Ez a cikk lépésről-lépésre nyilvános létrehozása [Load Balancer szabványos](https://aka.ms/azureloadbalancerstandard) a zónaredundáns időtúllépést IP szabványos nyilvános cím segítségével. Egy egyetlen előtérbeli IP-cím egy szabványos terheléselosztón zónaredundáns alapértelmezés szerint.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
 Rendelkezésre állási zónák támogatása select Azure-erőforrások és a régiók és a virtuális gép mérete családok érhető el. További információ az első lépések, és mely Azure-erőforrások, régiók és virtuális gép mérete családok megpróbálhatja a rendelkezésre állási zónákat, lásd: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-zone-redundant-load-balancer"></a>A zóna redundáns terheléselosztó létrehozása

1. Egy böngészőből keresse meg az Azure-portálon: [ http://portal.azure.com ](http://portal.azure.com) és az Azure-fiókkal történő bejelentkezés.
2. A képernyő bal felső oldalán válassza ki **hozzon létre egy erőforrást** > **hálózati** > **terheléselosztóhoz.**
3. Az a **létrehozás terheléselosztó** lap **neve** típus **myLoadBalancer**.
4. A **Típus** alatt válassza ki a **Nyilvános** elemet.
5. A Termékváltozat, válassza a **szabványos**.
6. Kattintson a **nyilvános IP-cím**, kattintson a **hozzon létre új**, majd a **nyilvános IP-cím létrehozása** lapon a nevét, típusát **myPublicIPStandard**.
    >[!NOTE] 
    > A nyilvános IP-ebben a lépésben létrehozott Standard Termékváltozat és zónaredundáns alapértelmezés szerint. 
8. A **hely**, jelölje be **keleti US2**, és kattintson a **OK**. Ekkor elindul a terheléselosztó üzembe helyezése, ami néhány perc alatt sikeresen befejeződik.

## <a name="next-steps"></a>További lépések
- További információ [szabványos terheléselosztó és a rendelkezésre állási zónák](load-balancer-standard-availability-zones.md).



