---
title: Create a Load Balancer with a zonal frontend - Azure portal
titleSuffix: Azure Load Balancer
description: Learn how to create a Standard Load Balancer with zonal frontend with the Azure portal
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
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-portal"></a>Create a Standard Load Balancer with zonal frontend using Azure portal

This article steps through creating a public [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) with a zonal frontend IP configuration. To understand how availability zones work with Standard Load Balancer, see [Standard Load Balancer and Availability zones](load-balancer-standard-availability-zones.md). 

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

> [!NOTE]
> Support for Availability Zones is available for select Azure resources and regions, and VM size families. For more information on how to get started, and which Azure resources, regions, and VM size families you can try availability zones with, see [Overview of Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-load-balancer-with-zonal-frontend-ip-address"></a>Create a load balancer with zonal frontend IP address

1. From a browser navigate to the Azure portal: [https://portal.azure.com](https://portal.azure.com) and login with your Azure account.
2. On the top left-hand side of the screen, select **Create a resource** > **Networking** > **Load Balancer.**
3. In the **Create load balancer** page, under **Name** type **myLoadBalancer**.
4. A **Típus** alatt válassza ki a **Nyilvános** elemet.
5. Under SKU, select **Standard**.
6. Click **Choose a Public IP address**, click **Create new**, and in **Create public IP address** page, under name, type **myPublicIPZonal**, for SKU, select **Standard**, for Availability zone, select **1**.
    
>[!NOTE] 
> The public IP created in this step is of Standard SKU by default.

1. For **Resource group**, click **Create new**, and then type **myResourceGroupZLB** as the name of the resource group.
1. For **Location**, select **West Europe**, and then click **OK**. Ekkor elindul a terheléselosztó üzembe helyezése, ami néhány perc alatt sikeresen befejeződik.

    ![create zone-redundant Standard Load Balancer with the Azure portal](./media/load-balancer-get-started-internet-availability-zones-zonal-portal/load-balancer-zonal-frontend.png)


## <a name="next-steps"></a>Következő lépések
- Learn more about [Standard Load Balancer and Availability zones](load-balancer-standard-availability-zones.md).



