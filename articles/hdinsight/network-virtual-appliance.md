---
title: Hálózati virtuális berendezés konfigurálása az Azure HDInsight
description: Megtudhatja, hogyan konfigurálhat számos további funkciót a hálózati virtuális berendezéshez az Azure HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: abedaf6f66bdd7aea36512c90bb5ee799f8e7a99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800944"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Hálózati virtuális berendezés konfigurálása az Azure HDInsight

> [!Important]
> A következő információkra **csak** akkor van szükség, ha nem Azure Firewall hálózati virtuális berendezést (NVA) szeretne konfigurálni.

A Azure Firewall automatikusan úgy van konfigurálva, hogy a gyakori fontos forgatókönyvek esetében lehetővé tegye a forgalmat. Egy másik hálózati virtuális készülék használata több további funkció konfigurálását is megköveteli. A hálózati virtuális berendezés konfigurálása során tartsa szem előtt a következő tényezőket:

* A szolgáltatás-végponttal kompatibilis szolgáltatások olyan szolgáltatási végpontokkal konfigurálhatók, amelyek megkerülik a NVA, általában a költségeket vagy a teljesítménnyel kapcsolatos megfontolásokat.
* Az IP-címek függőségei nem HTTP/S forgalomra vonatkoznak (TCP-és UDP-forgalom).
* A teljes tartománynév HTTP/HTTPS-végpontok a NVA-eszközön is megadhatók.
* Rendelje hozzá a HDInsight-alhálózathoz létrehozott útválasztási táblázatot.

## <a name="service-endpoint-capable-dependencies"></a>Szolgáltatási végpontok számára alkalmas függőségek

A következő szolgáltatási végpontok közül egyet vagy többet is engedélyezhet, amelyek a NVA megkerülését eredményezik. Ez a lehetőség nagy mennyiségű adatátvitelhez hasznos, ha a költségeket és a teljesítmény-optimalizálást is lehetővé teszi. 

| **Végpont** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP-címek függőségei

| **Végpont** | **Részletek** |
|---|---|
| [Itt](hdinsight-management-ip-addresses.md) közzétett IP-címek | Ezek az IP-címek a HDInsight-ellenőrzési helyre vonatkoznak, és a UDR kell szerepelniük az aszimmetrikus útválasztás elkerüléséhez |
| HRE – DS magánhálózati IP-címek | Csak az ESP-fürtökhöz szükséges|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-függőségek

> [!Important]
> Az alábbi lista csak néhányat ad a legfontosabb FQDN-nek. A NVA [ezen a fájlban](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)való konfigurálásához teljes listát kaphat a teljes tartománynevek (elsősorban az Azure Storage és a Azure Service Bus) teljes listájáról. Ezeket a függőségeket a HDInsight vezérli a fürt sikeres létrehozása érdekében.

| **Végpont**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>További lépések

* [Tűzfal használata a kimenő forgalom korlátozására](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight virtuális hálózati architektúra](hdinsight-virtual-network-architecture.md)
