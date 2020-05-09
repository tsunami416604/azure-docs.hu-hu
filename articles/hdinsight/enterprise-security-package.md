---
title: Enterprise Security Package az Azure HDInsight
description: Ismerje meg az Azure HDInsight Enterprise Security Package összetevőit és verzióit.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 07210350826001bd3e0be6e04be211c9de43695a
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997255"
---
# <a name="enterprise-security-package-for-azure-hdinsight"></a>Enterprise Security Package az Azure HDInsight

A vállalati biztonság egy opcionális csomag, amelyet hozzáadhat a HDInsight-fürthöz a fürt létrehozása munkafolyamat részeként. A Enterprise Security Package a következőket támogatja:

* Active Directory integrációja a hitelesítéshez.

    Korábban létrehozta a HDInsight-fürtöket a helyi rendszergazda felhasználóval és a helyi SSH-felhasználóval. A helyi rendszergazda felhasználó elérheti az összes fájlt, mappát, táblát és oszlopot.  A Enterprise Security Package lehetővé teszi a szerepköralapú hozzáférés-vezérlést a HDInsight és a Active Directory integrálásával. Ide tartozik a helyszíni Active Directory, Azure Active Directory Domain Services. Vagy Active Directory a IaaS virtuális gépen. A fürt tartományi rendszergazdája engedélyezheti a felhasználóknak, hogy saját vállalati (tartományi) felhasználónevet és jelszót használjanak.

    További információkért lásd:

    * [Bevezetés a Apache Hadoop biztonságba a tartományhoz csatlakoztatott HDInsight-fürtökkel](./domain-joined/hdinsight-security-overview.md)

    * [Azure-tartományhoz csatlakoztatott Apache Hadoop-fürtök tervezése a HDInsight-ben](./domain-joined/apache-domain-joined-architecture.md)

    * [Tartományhoz csatlakoztatott tesztkörnyezet konfigurálása](./domain-joined/apache-domain-joined-configure.md)

    * [Tartományhoz csatlakoztatott HDInsight-fürtök konfigurálása Azure Active Directory Domain Services használatával](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

* Adathozzáférés engedélyezése

  * Integráció az Apache Ranger-nal a kaptár, a Spark SQL és a fonalas várólisták engedélyezéséhez.
  * Megadhatja a fájlok és mappák hozzáférés-vezérlését.

    További információ: [Apache Hive házirendek konfigurálása a tartományhoz csatlakoztatott HDInsight](./domain-joined/apache-domain-joined-run-hive.md)

* Tekintse meg a naplókat a hozzáférések és a konfigurált szabályzatok figyeléséhez.

## <a name="supported-cluster-types"></a>Támogatott fürtök típusai

Jelenleg csak a következő típusú fürtök támogatják a Enterprise Security Package:

* Hadoop (csak HDInsight 3,6)
* Spark
* Kafka
* HBase
* Interaktív lekérdezés

## <a name="support-for-azure-data-lake-storage"></a>Azure Data Lake Storage támogatása

A Enterprise Security Package az elsődleges tárolóként és a kiegészítő tárolóként is támogatja a Azure Data Lake Storage használatát.

## <a name="pricing-and-service-level-agreement-sla"></a>Díjszabás és szolgáltatói szerződés (SLA)

További információ a Enterprise Security Package díjszabásáról és SLA-ról: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="next-steps"></a>További lépések

* [Fürt beállítása Apache Hadoop, Spark és más HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Munkahelyi Apache Hadoop HDInsight Windows rendszerű SZÁMÍTÓGÉPRŐL](hdinsight-hadoop-windows-tools.md)
* [Az Azure HDInsight-verziókhoz tartozó Hortonworks kibocsátási megjegyzések](./hortonworks-release-notes.md)
* [Apache-összetevők a HDInsight](./hdinsight-component-versioning.md)