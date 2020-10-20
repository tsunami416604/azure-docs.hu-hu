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
ms.openlocfilehash: 14a41365640439ff99861bbb22cc04a40f35da5e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222280"
---
# <a name="security-options-for-hive-in-azure-hdinsight"></a>Biztonsági beállítások a Kaptárhoz az Azure HDInsight

Ez a dokumentum ismerteti a kaptár ajánlott biztonsági beállításait a HDInsight-ben. Ezek a beállítások az Ambari használatával konfigurálhatók.

!["Biztonsági beállítások a Kaptárhoz"](./media/hdinsight-security-options-for-hive/security-options-hive.png "Biztonsági beállítások a Kaptárhoz")

## <a name="hiveserver2-authentication"></a>HiveServer2-hitelesítés

Standard szintű fürtök esetén a HiveServer2-hitelesítés javasolt beállítása az alapértelmezett érték, amely nincs. A hitelesítés engedélyezéséhez javasoljuk, hogy frissítsen egy [ESP](https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview) (Enterprise Security Package) fürtre. 

ESP-fürtök esetén a [Kerberos](https://web.mit.edu/Kerberos/) -hitelesítés alapértelmezés szerint engedélyezve van. A csatlakoztatható hitelesítési modulok (PAM) és az egyéni hitelesítési sémák nem támogatottak.

## <a name="hiveserver2-authorization"></a>HiveServer2-hitelesítés

Standard szintű fürtök esetében az alapértelmezett beállítás nincs. A [SqlStdAuth (SQL-szabványokon alapuló hitelesítés)](https://cwiki.apache.org/confluence/display/Hive/SQL+Standard+based+hive+authorization) engedélyezve lehet. A standard fürtök esetében nem támogatott az [Apache rangern](https://ranger.apache.org/) keresztüli engedélyezés. Javasoljuk, hogy a Ranger-hitelesítéshez egy ESP-fürtöt frissítsen. 

Az ESP-fürtök esetében a Ranger engedélyezése alapértelmezés szerint engedélyezve van. 


## <a name="ssl-encryption-for-hiveserver2"></a>SSL-titkosítás a HiveServer2

A Hiveserver2 SSL engedélyezése standard vagy ESP fürtök esetén nem ajánlott. Az SSL engedélyezve van az átjárón. Az [átvitel során](https://docs.microsoft.com/azure/hdinsight/domain-joined/encryption-in-transit) engedélyezhető a fürt csomópontjai közötti kommunikáció titkosítása [Internet Protocol biztonság (IPSec)](https://en.wikipedia.org/wiki/IPsec)használatával.


## <a name="next-steps"></a>Következő lépések
* [Az HiveServer2-hitelesítés áttekintése](https://cwiki.apache.org/confluence/display/Hive/Setting+up+HiveServer2#SettingUpHiveServer2-Authentication/SecurityConfiguration)
* [Az HiveServer2-engedélyezés áttekintése](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Authorization#:~:text=%20Overview%20of%20Authorization%20Modes%20%201%201,and%20Apache%20Sentry%20are%20apache%20projects...%20More%20)
* [SQL-szabványon alapuló struktúra engedélyezésének engedélyezése](https://community.cloudera.com/t5/Community-Articles/Getting-started-with-SQLStdAuth/ta-p/244263)
* [Apache Ranger a kaptárral](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-run-hive#:~:text=Create%20Hive%20ODBC%20data%20source%20%20%20,Enter%20hiveuser1%40contoso158.onmicrosoft.c%20...%20%205%20more%20rows%20)
