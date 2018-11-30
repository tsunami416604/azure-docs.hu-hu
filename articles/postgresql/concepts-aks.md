---
title: Az Azure Kubernetes Service (AKS) csatlakoztatása az Azure Database for postgresql-hez
description: További tudnivalók az Azure Kubernetes Service csatlakoztatása az Azure Database for postgresql-hez
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.date: 11/27/2018
ms.topic: article
ms.openlocfilehash: 86474fe612fb93f3a5853f9fea98eb9ab2dd00e5
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336979"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql"></a>Csatlakozás az Azure Kubernetes Service és az Azure Database for postgresql-hez

Az Azure Kubernetes Service (AKS) biztosít egy felügyelt Kubernetes-fürtöt az Azure-ban is használhatja. Az alábbiakban néhány lehetőség használatakor figyelembe kell AKS és Azure Database for PostgreSQL együtt alkalmazás létrehozása.


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


## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Nyissa meg a Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) lehetővé teszi a közvetlenül a Kubernetes vagy a Cloud Foundry Azure-szolgáltatások kiépítése. Ez egy [Open Service Broker API](https://www.openservicebrokerapi.org/) megvalósítása az Azure-hoz.

Az OSBA hozzon létre egy Azure Database for PostgreSQL-kiszolgálóhoz, és kösse az AKS-fürt Kubernetes általi natív nyelv használatával. Megtudhatja, hogyan használandó OSBA és az Azure Database for postgresql-hez együtt a [OSBA Github-oldalon](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Kapcsolatkészletezést
Egy csatlakozáskészlet minimálisra csökkenti a költségek és a kapcsolódó létrehozását és az adatbázishoz való csatlakozás új záró idő. A készlet, amelyek felhasználhatók kapcsolatok gyűjteménye. 

Nincsenek több kapcsolat poolers PostgreSQL használható. Ezek közül az egyik [PgBouncer](https://pgbouncer.github.io/). A Microsoft Tárolójegyzéket biztosítunk egy egyszerűsített tárolóalapú PgBouncer, amelyek segítségével az oldalkocsi készlet az AKS az Azure Database for postgresql-hez. Látogasson el a [docker hub oldalon](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) megtudhatja, hogyan, hogy eléri és használja ezt a képet. 


## <a name="next-steps"></a>További lépések
-  [Az Azure Kubernetes Service-fürt létrehozása](../aks/kubernetes-walkthrough.md)
