---
title: Load Balancer létrehozása egy zóna-előtérbeli felülettel – Azure Portal
titleSuffix: Azure Load Balancer
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
ms.openlocfilehash: 82a773b279780bc4eb784fa107d6b15bd0ff2672
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225337"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>standard Load Balancer létrehozása a zóna-előtérben a Azure Portal használatával

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre egy nyilvános [standard Load Balancer](https://aka.ms/azureloadbalancerstandard) egy zóna-előtérbeli IP-konfigurációval. Annak megismeréséhez, hogy a rendelkezésre állási zónák hogyan működnek a standard Load Balancerokkal, tekintse meg a [standard Load Balancer és a rendelkezésre állás](load-balancer-standard-availability-zones.md) 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> A rendelkezésre állási zónák támogatása az Azure-erőforrásokhoz, és a régiók és a virtuális gép méretcsaládjai érhető el. További információ az első lépésekről, valamint arról, hogy az Azure-erőforrások,-régiók és a virtuális gépek méretének családja milyen módon próbálhatja ki a rendelkezésre állási zónákat: [Availability Zones áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>A zónaszintű előtérbeli IP-címmel rendelkező terheléselosztó létrehozása

1. Egy böngészőből navigáljon a Azure Portal: [https://portal.azure.com](https://portal.azure.com) , és jelentkezzen be az Azure-fiókjával.
2. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **Load Balancer lehetőséget.**
3. A **Load Balancer létrehozása** lapon a **név** mezőbe írja be a következőt: **myLoadBalancer**.
4. A **Típus** alatt válassza ki a **Nyilvános** elemet.
5. Az SKU területen válassza a **standard**lehetőséget.
6. Kattintson **a nyilvános IP-cím választása**lehetőségre, kattintson az **új létrehozása**elemre, majd a **nyilvános IP-cím létrehozása** lap név területén írja be a **myPublicIPZonal**, az SKU, a **standard**, a rendelkezésre állási zóna lehetőséget, majd válassza az **1**elemet.
    
>[!NOTE] 
> A nyilvános IP-cím ebben a lépésben létrehozott része Standard termékváltozat alapértelmezés szerint.

1. Az **erőforráscsoport**területen kattintson az **új létrehozása**elemre, majd írja be a **myResourceGroupZLB** nevet az erőforráscsoport neveként.
1. A **hely**mezőben válassza a **Nyugat-Európa**lehetőséget, majd kattintson **az OK**gombra. Ekkor elindul a terheléselosztó üzembe helyezése, ami néhány perc alatt sikeresen befejeződik.

    ![zóna létrehozása – redundáns standard Load Balancer a Azure Portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>További lépések
- További információ a [standard Load Balancer és a rendelkezésre állási zónákról](load-balancer-standard-availability-zones.md).



