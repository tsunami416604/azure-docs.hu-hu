---
title: "Azure virtuális hálózat az tárolókat |} Microsoft Docs"
description: "További tudnivalók a CNI Kubernetes fürtök beépülő modul, amely lehetővé teszi a tárolók kommunikál egymással és más erőforrások, a virtuális hálózat."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2017
---
# <a name="container-networking"></a>Hálózati tároló

Azure biztosít egy [tároló hálózati illesztő (CNI) beépülő modul](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) , amely lehetővé teszi, akkor helyezheti üzembe és felügyelheti a saját Kubernetes fürt natív Azure hálózati alkalmas. A beépülő modul, alapértelmezés szerint engedélyezve van, ha a fürtök üzembe helyezése Kubernetes a [Azure tároló szolgáltatás vezérlőprogramja](https://github.com/Azure/acs-engine) (vagy ACS-motor).

## <a name="networking-capabilities"></a>Hálózatkezelési lehetőségek

Tárolók egy virtuális hálózatot kínál, például a lehetőségek széles skáláját használhatja:
-   Külön virtuális hálózat létrehozása a fürt számára, vagy egy meglévő virtuális hálózat a fürt központi telepítése. 
-   A fürt minden tok a virtuális hálózaton belül egy IP-címet kap- és a fürt más három munkaállomás-csoporttal és a virtuális hálózat a virtuális gépek közvetlenül kommunikálhatnak. 
-   Egy pod kapcsolódhatnak más három munkaállomás-csoporttal és a virtuális gépek nincsenek társviszonyban, virtuális hálózatok és a helyszíni hálózatokhoz, ExpressRoute- és VPN-kapcsolatok pont-pont keresztül. A helyszíni erőforrások jelezheti három munkaállomás-csoporttal. 
-   Az interneten keresztül az Azure Load Balancer egy Kubernetes szolgáltatást is elérhetővé teheti.  
-   Olyan alhálózatot, amely Szolgáltatásvégpontok engedélyezve van a három munkaállomás-csoporttal biztonságosan csatlakozni tud az Azure-szolgáltatásokhoz (tárolási és SQL-adatbázis, a példában).
-   Felhasználó által definiált útvonalak segítségével három munkaállomás-csoporttal forgalom átirányítása egy virtuális hálózati berendezések. 
-   Három munkaállomás-csoporttal az interneten lévő nyilvános erőforrásait is elérik.
-   Egy pod rendelhet egy nyilvános IP-címet, amely társítva van egy DNS-név lehet.
 
## <a name="limits"></a>Korlátok
A beépülő modul használata esetén telepítheti Kubernetes fürtben, és legfeljebb 250 három munkaállomás-csoporttal és a teljes korlátja 16 000 három munkaállomás-csoporttal fürtönként, csomópontonként legfeljebb 4000 csomópontok.

## <a name="constraints"></a>Korlátozások
- A beépülő modul nincs engedélyezve a Kubernetes fürtben való telepítésekor a [Azure tároló szolgáltatás (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Kubernetes fürt.
- Nincs natív támogatás Kubernetes hálózati házirendek, beleértve a DNS vagy a hozzáférési házirendek.
- A beépülő modul nem támogatja a-pod hálózati házirendek.

## <a name="pricing"></a>Díjszabás
Nincs a CNI beépülő modul használata díjmentes.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [Kubernetes fürt központi telepítése](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md) a a [saját virtuális hálózati](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni).
