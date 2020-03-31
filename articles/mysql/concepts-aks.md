---
title: Csatlakozás az Azure Kubernetes szolgáltatáshoz – Azure Database for MySQL
description: Tudnivalók az Azure Kubernetes-szolgáltatás és a MySQL Azure Database szolgáltatással való összekapcsolásáról
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9e020d34b6cfb8117ccff1114cc938c966126b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537278"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Az Azure Kubernetes szolgáltatás és a MySQL Azure-adatbázisának csatlakoztatása

Az Azure Kubernetes-szolgáltatás (AKS) egy felügyelt Kubernetes-fürtöt biztosít, amelyet az Azure-ban használhat. Az alábbiakban néhány lehetőség, hogy fontolja meg, ha az AKS és az Azure Database for MySQL együtt egy alkalmazás létrehozásához.


## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés
Az AKS-fürtben gyorsított hálózattal rendelkező virtuális gépek használata. Ha a virtuális gépen engedélyezve van a gyorsított hálózatkezelés, kisebb a késés, csökken a jitter és a processzorkihasználtság a virtuális gépen. További információ a gyorsított hálózatkezelés működéséről, a támogatott operációs rendszer verzióiról és a [linuxos](../virtual-network/create-vm-accelerated-networking-cli.md)virtuálisgép-példányokról.

2018 novemberétől az AKS támogatja a támogatott virtuálisgép-példányok gyorsított hálózatba való leküzdését. A gyorsított hálózatkezelés alapértelmezés szerint engedélyezve van az új AKS-fürtökön, amelyek ezeket a virtuális gépeket használják.

Megerősítheti, hogy az AKS-fürt felgyorsította-e a hálózatkezelést:
1. Nyissa meg az Azure Portalon, és válassza ki az AKS-fürtöt.
2. Válassza a Properties (Tulajdonságok) lapot.
3. Másolja az **Infrastruktúra erőforráscsoport nevére.**
4. A portál keresősávja segítségével keresse meg és nyissa meg az infrastruktúra erőforráscsoportot.
5. Válasszon ki egy virtuális gép az erőforráscsoportban.
6. Nyissa meg a virtuális gép **hálózatozása** lapot.
7. Ellenőrizze, hogy a **gyorsított hálózatkezelés** "Engedélyezve" van-e.

Vagy az Azure CLI-n keresztül a következő két parancs használatával:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
A kimenet az AKS által létrehozott erőforráscsoport lesz, amely a hálózati illesztőt tartalmazza. Vegye ki a "nodeResourceGroup" nevet, és használja a következő parancsban. **Az EnableAcceleratedNetworking** értéke igaz vagy hamis:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Az Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) lehetővé teszi az Azure-szolgáltatások kiépítését közvetlenül a Kubernetes-től vagy a Cloud Foundry-tól. Ez egy [nyílt szolgáltatásközvetítő API-implementáció](https://www.openservicebrokerapi.org/) az Azure-hoz.

Az OSBA segítségével létrehozhat egy Azure Database for MySQL-kiszolgálót, és a Kubernetes anyanyelvén kötheti az AKS-fürthöz. Ismerje meg, hogyan használhatja együtt az OSBA és az Azure Database for MySQL együttes használatát az [OSBA GitHub oldalán.](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md) 



## <a name="next-steps"></a>További lépések
- [Azure Kubernetes-szolgáltatásfürt létrehozása](../aks/kubernetes-walkthrough.md)
- Ismerje meg, hogyan [telepítheti a WordPress-t egy Helm-diagramról az OSBA és az Azure Database for MySQL használatával](../aks/integrate-azure.md)
