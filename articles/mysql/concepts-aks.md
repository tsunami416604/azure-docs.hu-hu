---
title: Csatlakozás az Azure Kubernetes Service (AKS) az Azure Database for MySQL
description: További tudnivalók az Azure Kubernetes Service csatlakoztatása az Azure Database for MySQL-hez
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/28/2018
ms.openlocfilehash: d9f2e26a2bc89329ca9038c666c0d960289e2670
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55485449"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Csatlakozás az Azure Kubernetes Service és az Azure Database for MySQL-hez

Az Azure Kubernetes Service (AKS) biztosít egy felügyelt Kubernetes-fürtöt az Azure-ban is használhatja. Az alábbiakban néhány lehetőség használatakor figyelembe kell AKS és Azure Database for MySQL együtt alkalmazás létrehozása.


## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés
A gyorsított hálózati üzemmódú alapjául szolgáló virtuális gépeinek az AKS-fürt használatát. Ha egy virtuális gépen engedélyezve van a gyorsított hálózatkezelés, nincs kisebb hálózati késést, végrehajtását alacsonyabb jitter és csökkent, a virtuális gép CPU-kihasználtság. Ismerje meg bővebben tájékozódhat a gyorsított hálózati működik, a támogatott operációs rendszerekről, és a Virtuálisgép-példányok esetében támogatott [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

A 2018 November AKS gyorsított hálózatkezelés támogatott Virtuálisgép-példányok használatát támogatja. A virtuális gépek használó új AKS-fürtök alapértelmezés szerint engedélyezve van a gyorsított hálózatkezeléssel.

Ellenőrizheti, hogy az AKS-fürt rendelkezik gyorsított hálózatkezelést:
1. Nyissa meg az Azure Portalon, és válassza ki az AKS-fürt.
2. Válassza a Tulajdonságok lapot.
3. Másolja ki a nevét a **infrastruktúra erőforráscsoport**.
4. A portál Keresősáv használatával keresse meg és nyissa meg az infrastruktúra erőforráscsoportot.
5. Válasszon egy virtuális Gépet az erőforráscsoport.
6. Nyissa meg a virtuális gép **hálózatkezelés** fülre.
7. Győződjön meg róla hogy **gyorsított hálózatkezelés** "Engedélyezve van."

Vagy az Azure CLI-n keresztül az alábbi két parancs használatával:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
A kimenet a létrehozott erőforráscsoportot, hogy az AKS hoz létre, amely tartalmazza a hálózati adapter lesz. "NodeResourceGroup" nevét, és a következő parancsot használhatja. **EnableAcceleratedNetworking** vagy lesz true vagy FALSE (hamis):
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Nyissa meg a Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) lehetővé teszi a közvetlenül a Kubernetes vagy a Cloud Foundry Azure-szolgáltatások kiépítése. Ez egy [Open Service Broker API](https://www.openservicebrokerapi.org/) megvalósítása az Azure-hoz.

Az OSBA hozzon létre egy Azure Database for MySQL-kiszolgálóhoz, és kösse az AKS-fürt Kubernetes általi natív nyelv használatával. Megtudhatja, hogyan használandó OSBA és az Azure Database for MySQL-hez együtt a [OSBA GitHub-oldalon](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>További lépések
- [Az Azure Kubernetes Service-fürt létrehozása](../aks/kubernetes-walkthrough.md)
- Ismerje meg, hogyan [WordPress telepítése egy Helm-diagramból OSBA és az Azure Database for MySQL használatával](../aks/integrate-azure.md)
