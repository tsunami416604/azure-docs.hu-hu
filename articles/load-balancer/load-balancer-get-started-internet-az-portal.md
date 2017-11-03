---
title: "Hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér Azure-portál használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér és az Azure portál"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: kumud
ms.openlocfilehash: e28983f6015f8dcaa546c20fd9bc8d986a15ab5d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-azure-portal"></a>Hozzon létre egy Load Balancer szabványos nyilvános zónaredundáns nyilvános IP-cím cím előtér Azure-portál használatával

Ez a cikk lépésről-lépésre nyilvános létrehozása [Load Balancer szabványos](https://aka.ms/azureloadbalancerstandard) a zónaredundáns időtúllépést IP szabványos nyilvános cím segítségével.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="register-for-availability-zones-load-balancer-standard-and-public-ip-standard-preview"></a>Standard Preview rendelkezésre állási zónák, a Load Balancer Standard és a nyilvános IP-cím regisztrálása
 
>[!NOTE]
[Terheléselosztó Standard Termékváltozat betöltése](https://aka.ms/azureloadbalancerstandard) jelenleg előzetes verzió. Az előzetes kiadás során a szolgáltatás rendelkezésre állása és megbízhatósága eltérő lehet az általánosan elérhető kiadásétól. További részletekért lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Használja a nyilvánosan elérhető [Load Balancer alapszintű Termékváltozat](load-balancer-overview.md) a termelés számára. 

> [!NOTE]
> Rendelkezésre állási zónák még csak előzetes verziójúak, és készen áll a fejlesztési és forgatókönyvek tesztelése. A select Azure-erőforrások és a régiók és a virtuális gép mérete családok támogatás érhető el. További információ az első lépések, és mely Azure-erőforrások, régiók és virtuális gép mérete családok megpróbálhatja a rendelkezésre állási zónákat, lásd: [rendelkezésre állási zónák áttekintése](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha támogatásra van szüksége, keresse fel a [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) fórumot, vagy [nyisson meg egy Azure támogatási jegyet](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  

Mielőtt kiválasztja, a zóna vagy zónaredundáns. lehetőséget az az előtérbeli nyilvános IP-címet a terheléselosztóhoz, akkor először el kell végeznie a lépéseket [regisztrálja a rendelkezésre állási zónák Preview](https://docs.microsoft.com/azure/availability-zones/az-overview).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure portálon, a https://portal.azure.com.

## <a name="create-a-zone-redundant-load-balancer"></a>A zóna redundáns terheléselosztó létrehozása

1. Egy böngészőből keresse fel az Azure Portalt: [http://portal.azure.com](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. A képernyő bal felső oldalán válassza ki **új** > **hálózati** > **terheléselosztóhoz.**
3. Az a ** hozza létre a belső terheléselosztót, **neve** típus **myPublicLB**.
4. A **Típus** alatt válassza ki a **Nyilvános** elemet.
5. A Termékváltozat, válassza a **(előzetes verzió) szabvány**.
6. Kattintson a **nyilvános IP-cím**, kattintson a **hozzon létre új**, a a **hozzon létre egy nyilvános IP-cím** lapon a nevét, típusát **myPublicIPStandard**, és a **rendelkezésre állási zóna (előzetes verzió)**, jelölje be **zónaredundáns**.
7. A **hely**, jelölje be **keleti US2**, és kattintson a **OK**. Ekkor elindul a terheléselosztó üzembe helyezése, ami néhány perc alatt sikeresen befejeződik.

    ![zónaredundáns Load Balancer szabványos létrehozása az Azure-portálon](./media/load-balancer-get-started-internet-az-portal/create-zone-redundant-load-balancer-standard.png)


## <a name="next-steps"></a>Következő lépések
- Megtudhatja, hogyan [hozzon létre egy nyilvános IP-cím egy rendelkezésre állási zónában](../virtual-network/create-public-ip-availability-zone-portal.md)



