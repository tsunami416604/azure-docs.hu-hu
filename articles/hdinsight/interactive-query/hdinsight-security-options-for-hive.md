---
title: Biztonsági beállítások a Kaptárhoz az Azure HDInsight
description: Biztonsági beállítások a Kaptárhoz a standard és az ESP fürtökben.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/02/2020
ms.openlocfilehash: 8573ba99b7aef13025b4f175640ac9583ad5a679
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545956"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Biztonsági beállítások a Kaptárhoz az Azure HDInsight

Ez a dokumentum ismerteti a kaptár ajánlott biztonsági beállításait a HDInsight-ben. Ezek a beállítások az Ambari használatával konfigurálhatók.

!["Biztonsági beállítások a Kaptárhoz"](./media/hdinsight-security-options-for-hive/security-options-hive.png "Biztonsági beállítások a Kaptárhoz")

## <a name="hiveserver2-authentication"></a>HiveServer2-hitelesítés

Standard szintű fürtök esetén a HiveServer2-hitelesítés javasolt beállítása az alapértelmezett érték, amely nincs. A hitelesítés engedélyezéséhez javasoljuk, hogy frissítsen egy [ESP](../domain-joined/hdinsight-security-overview.md) (Enterprise Security Package) fürtre. 

ESP-fürtök esetén a [Kerberos](https://web.mit.edu/Kerberos/) -hitelesítés alapértelmezés szerint engedélyezve van. A csatlakoztatható hitelesítési modulok (PAM) és az egyéni hitelesítési sémák nem támogatottak.

## <a name="hiveserver2-authorization"></a>HiveServer2-hitelesítés

Standard szintű fürtök esetében az alapértelmezett beállítás nincs. A [SqlStdAuth (SQL-szabványokon alapuló hitelesítés)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) engedélyezve lehet. A standard fürtök esetében nem támogatott az [Apache rangern](https://ranger.apache.org/) keresztüli engedélyezés. Javasoljuk, hogy a Ranger-hitelesítéshez egy ESP-fürtöt frissítsen. 

Az ESP-fürtök esetében a Ranger engedélyezése alapértelmezés szerint engedélyezve van. 


## <a name="ssl-encryption-for-hiveserver2"></a>SSL-titkosítás a HiveServer2

A Hiveserver2 SSL engedélyezése standard vagy ESP fürtök esetén nem ajánlott. Az SSL engedélyezve van az átjárón. Az [átvitel során](../domain-joined/encryption-in-transit.md) engedélyezhető a fürt csomópontjai közötti kommunikáció titkosítása [Internet Protocol biztonság (IPSec)](https://en.wikipedia.org/wiki/IPsec)használatával.


## <a name="next-steps"></a>Következő lépések
* [Az HiveServer2-hitelesítés áttekintése](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Az HiveServer2-engedélyezés áttekintése](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization#:~:text=%20Overview%20of%20Authorization%20Modes%20%201%201,and%20Apache%20Sentry%20are%20apache%20projects...%20More%20)
* [SQL-szabványon alapuló struktúra engedélyezésének engedélyezése](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger a kaptárral](../domain-joined/apache-domain-joined-run-hive.md)
