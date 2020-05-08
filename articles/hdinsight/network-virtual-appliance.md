---
title: Hálózati virtuális berendezés konfigurálása az Azure HDInsight
description: Megtudhatja, hogyan konfigurálhat számos további funkciót a hálózati virtuális berendezéshez az Azure HDInsight-ben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864707"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Hálózati virtuális berendezés konfigurálása az Azure HDInsight

> [!Important]
> A következő információkra **csak** akkor van szükség, ha nem Azure Firewall hálózati virtuális berendezést (NVA) szeretne konfigurálni.

A Azure Firewall automatikusan úgy van konfigurálva, hogy a gyakori fontos forgatókönyvek esetében lehetővé tegye a forgalmat. Egy másik hálózati virtuális készülék használata több további funkció konfigurálását is megköveteli. A hálózati virtuális berendezés konfigurálása során tartsa szem előtt a következő tényezőket:

* A szolgáltatási végponttal kompatibilis szolgáltatásokat a szolgáltatási végpontokkal kell konfigurálni.
* Az IP-címek függőségei nem HTTP/S forgalomra vonatkoznak (TCP-és UDP-forgalom).
* Az FQDN HTTP/HTTPS-végpontok a NVA-eszközön helyezhetők el.
* A helyettesítő HTTP/HTTPS-végpontok olyan függőségek, amelyek számos minősítőtől függően változhatnak.
* Rendelje hozzá a HDInsight-alhálózathoz létrehozott útválasztási táblázatot.

## <a name="service-endpoint-capable-dependencies"></a>Szolgáltatási végpontok számára alkalmas függőségek

| **Végpont** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>IP-címek függőségei

| **Végpont** | **Részletek** |
|---|---|
| \*: 123 | NTP órajel-ellenőrzési. A forgalom a 123-es porton több végponton van bejelölve |
| [Itt](hdinsight-management-ip-addresses.md) közzétett IP-címek | Ezek az IP-címek a HDInsight szolgáltatás |
| HRE – DS magánhálózati IP-címek ESP-fürtökhöz |
| \*: 16800 KMS Windows-aktiváláshoz |
| \*12000 Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-függőségek

> [!Important]
> Az alábbi lista csak néhányat ad a legfontosabb FQDN-nek. További teljes tartományneveket (elsősorban az Azure Storage-t és a Azure Service Bust) a [fájl](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)NVA konfigurálásához érhet el.

| **Végpont**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>További lépések

* [Tűzfal használata a kimenő forgalom korlátozására](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight virtuális hálózati architektúra](hdinsight-virtual-network-architecture.md)
