---
title: Az Azure-alkalmazás tűzfalszabályok konfigurálása az SQL teljes tartománynevek
description: Ebből a cikkből elsajátíthatja az SQL teljes tartománynevek konfigurálása az Azure application tűzfalszabályokat.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/11/2019
ms.author: victorh
ms.openlocfilehash: e188a5dda8f936ad369aa2b9222bc726bb0d6a5e
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786587"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Az Azure-alkalmazás tűzfalszabályok konfigurálása az SQL teljes tartománynevek

> [!IMPORTANT]
> Az Azure application tűzfalszabályok az SQL teljes tartománynevek jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Most konfigurálhatja az Azure application tűzfalszabályok az SQL teljes tartománynevek. Ez lehetővé teszi, hogy korlátozza a hozzáférést a virtuális hálózatok csak a megadott SQL server-példányok számára.

Az SQL teljes tartományneveket szűrheti a forgalmat:

- Az Azure SQL Database vagy az Azure SQL Data Warehouse a virtuális hálózatok. Példa: Való hozzáférés engedélyezése csak az *sql-server1.database.windows.net*.
- A helyszínről az Azure SQL felügyelt példányai vagy az SQL IaaS fut a virtuális hálózatok.
- A küllő-az-küllő Azure SQL felügyelt példányai vagy az SQL IaaS fut a virtuális hálózatok.

A nyilvános előzetes során SQL teljesen minősített Tartományneve, szűrés támogatott [proxyt módú](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) csak (port 1433-as). Ha használja az alapértelmezett átirányítási mód az SQL, hozzáférés az SQL szolgáltatáscímke részeként szűrheti [hálózati szabályai](overview.md#network-traffic-filtering-rules).
Ha nem alapértelmezett portok az SQL IaaS-forgalmat használ, konfigurálhatja azokat a portokat a tűzfalszabályok a.

> [!NOTE]
> Jelenleg az SQL teljes tartománynevek alkalmazás szabályok érhető el minden régióban az Azure CLI-vel, a REST és a sablonok használatával. A portál kezelőfelülete ad hozzá régiókat Növekményesen, és lesz elérhető az összes régióban a bevezetés befejezésekor.

## <a name="configure-using-azure-cli"></a>Konfigurálja az Azure CLI használatával

1. Üzembe helyezése egy [Azure CLI használatával Azure tűzfal](deploy-cli.md).
2. Ha Ön az Azure SQL Database, az SQL Data Warehouse vagy SQL felügyelt példánya forgalom szűrésére, győződjön meg arról, az SQL csatlakozási mód beállítása **Proxy**. Váltás SQL-csatlakozási mód kapcsolatban lásd: [Azure SQL-kapcsolati architektúra](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* mód képest további késést eredményezhet *átirányítási*. Ha azt szeretné, hogy tovább használhassa a átirányítási mód, amely az alapértelmezett Azure-ban kapcsolódó ügyfelek számára, szűrheti az SQL-hozzáférés [szolgáltatáscímke](service-tags.md) a tűzfal [hálózati szabályai](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Egy alkalmazás szabály konfigurálása egy SQL server elérésének engedélyezése az SQL teljes-tartománynév:

   ```azurecli
   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Konfigurálja az Azure portal használatával
1. Üzembe helyezése egy [Azure CLI használatával Azure tűzfal](deploy-cli.md).
2. Ha Ön az Azure SQL Database, az SQL Data Warehouse vagy SQL felügyelt példánya forgalom szűrésére, győződjön meg arról, az SQL csatlakozási mód beállítása **Proxy**. Váltás SQL-csatlakozási mód kapcsolatban lásd: [Azure SQL-kapcsolati architektúra](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy). 

   > [!NOTE]
   > SQL *proxy* mód képest további késést eredményezhet *átirányítási*. Ha azt szeretné, hogy tovább használhassa a átirányítási mód, amely az alapértelmezett Azure-ban kapcsolódó ügyfelek számára, szűrheti az SQL-hozzáférés [szolgáltatáscímke](service-tags.md) a tűzfal [hálózati szabályai](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Adja hozzá az alkalmazás a szabály a megfelelő protokoll, port és az SQL teljes Tartományneve, majd **mentése**.
   ![alkalmazás-szabályt az SQL teljes Tartományneve](media/sql-fqdn-filtering/application-rule-sql.png)
4. Hozzáférés az SQL virtuális gépről egy virtuális hálózatban, amely szűri a forgalmat a tűzfalon keresztül. 
5. Ellenőrizze, hogy [Azure tűzfal-naplókon](log-analytics-samples.md) bemutatják a forgalom engedélyezve van.

## <a name="next-steps"></a>További lépések

Ismerje meg az SQL-proxy és az átirányítási módok [Azure SQL database kapcsolati architektúra](../sql-database/sql-database-connectivity-architecture.md).