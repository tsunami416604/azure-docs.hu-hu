---
title: Kapcsolódás az Azure Kubernetes szolgáltatáshoz – Azure Database for MySQL
description: Tudnivalók az Azure Kubernetes szolgáltatás az Azure Database for MySQL-vel való csatlakoztatásáról
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 9e020d34b6cfb8117ccff1114cc938c966126b77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537278"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Az Azure Kubernetes szolgáltatás és a Azure Database for MySQL csatlakoztatása

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes-fürtöt biztosít, amelyet az Azure-ban használhat. Az alábbiakban néhány olyan lehetőség közül választhat, amely az AK-t használja, és Azure Database for MySQL együtt egy alkalmazás létrehozásához.


## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés
Gyorsított hálózatkezeléssel rendelkező mögöttes virtuális gépek használata az AK-fürtben. Ha a gyorsított hálózatkezelés engedélyezve van egy virtuális gépen, alacsonyabb a késés, a csökkent jitter és a CPU-kihasználtság csökkent a virtuális gépen. További információ a gyorsított hálózatkezelés működéséről, a támogatott operációsrendszer-verziókról és a [Linux](../virtual-network/create-vm-accelerated-networking-cli.md)rendszerhez támogatott VM-példányokról.

Az AK november 2018-én támogatja a gyorsított hálózatkezelést a támogatott VM-példányokon. A gyorsított hálózatkezelés alapértelmezés szerint engedélyezve van a virtuális gépeket használó új AK-fürtökön.

Megerősítheti, hogy az AK-fürt felgyorsult hálózattal rendelkezik-e:
1. Nyissa meg a Azure Portal, és válassza ki az AK-fürtöt.
2. Válassza a Properties (Tulajdonságok) lapot.
3. Másolja az **infrastruktúra-erőforráscsoport**nevét.
4. Az infrastruktúra-erőforráscsoport megkereséséhez és megnyitásához használja a portál keresési sávját.
5. Válasszon ki egy virtuális gépet az adott erőforráscsoporthoz.
6. Nyissa meg a virtuális gép **hálózatkezelés** lapját.
7. Győződjön meg arról, hogy a **gyorsított hálózatkezelés** engedélyezve van-e.

Vagy az Azure CLI-n keresztül az alábbi két parancs használatával:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
A kimenet a létrehozott erőforráscsoport, amely a hálózati adaptert tartalmazó AK-t hozza létre. Adja meg a "nodeResourceGroup" nevet, és használja a következő parancsban. A **EnableAcceleratedNetworking** értéke TRUE (igaz) vagy FALSE (hamis) lesz:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[A nyílt Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) lehetővé teszi az Azure-szolgáltatások kiépítését közvetlenül a Kubernetes vagy a Cloud Foundryból. Ez egy [nyílt Service Broker API](https://www.openservicebrokerapi.org/) -implementáció az Azure-hoz.

A OSBA-mel létrehozhat egy Azure Database for MySQL-kiszolgálót, és összekapcsolhatja azt az AK-fürttel az Kubernetes natív nyelv használatával. Ismerje meg, hogyan használható a OSBA és a Azure Database for MySQL együtt a [OSBA GitHub oldalon](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>További lépések
- [Azure Kubernetes Service-fürt létrehozása](../aks/kubernetes-walkthrough.md)
- Ismerje meg, hogyan [telepítheti a WordPresst egy Helm-diagramon a OSBA és a Azure Database for MySQL használatával](../aks/integrate-azure.md)
