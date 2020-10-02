---
title: Hálózati virtuális berendezés konfigurálása az Azure HDInsight
description: Megtudhatja, hogyan konfigurálhat számos további funkciót a hálózati virtuális berendezéshez az Azure HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 407160a5c315844003db4c5e371a03e6e25d2694
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630933"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Hálózati virtuális berendezés konfigurálása az Azure HDInsight

> [!Important]
> A következő információkra **csak** akkor van szükség, ha nem Azure Firewall hálózati virtuális berendezést (NVA) szeretne konfigurálni.

A Azure Firewall automatikusan úgy van konfigurálva, hogy a gyakori fontos forgatókönyvek esetében lehetővé tegye a forgalmat. Egy másik hálózati virtuális készülék használata több további funkció konfigurálását is megköveteli. A hálózati virtuális berendezés konfigurálása során tartsa szem előtt a következő tényezőket:

* A szolgáltatás-végponttal kompatibilis szolgáltatások olyan szolgáltatási végpontokkal konfigurálhatók, amelyek megkerülik a NVA, általában a költségeket vagy a teljesítménnyel kapcsolatos megfontolásokat.
* Az IP-címek függőségei nem HTTP/S forgalomra vonatkoznak (TCP-és UDP-forgalom).
* Az FQDN HTTP/HTTPS-végpontok jóváhagyható a NVA-eszközön.
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
> Az alábbi lista csak néhány teljes tartománynevet ad meg, amelyekre szükség lehet az operációs rendszer és a biztonság javításához vagy a tanúsítvány érvényességéhez a fürt létrehozása és a fürt műveleteinek élettartama során. A NVA ebben a [fájlban](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)való konfigurálásához lekérheti a teljes tartománynevek függőségeinek (elsősorban az Azure Storage és a Azure Service Bus) listáját. Ezeket a függőségeket a HDInsight erőforrás-szolgáltató (RP) használja a fürtök sikeres létrehozásához és figyeléséhez/kezeléséhez. Ilyenek például a telemetria/diagnosztikai naplók, a metaadatok kiépítése, a fürthöz kapcsolódó konfigurációk, parancsfájlok, ARM-sablonok stb. Előfordulhat, hogy a teljes tartománynév-függőségi lista a későbbi HDIngisht-frissítések felszabadításával változhat.

| **Végpont**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com:80                                                      |

## <a name="next-steps"></a>További lépések

* [Tűzfal használata a kimenő forgalom korlátozására](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight virtuális hálózati architektúra](hdinsight-virtual-network-architecture.md)
