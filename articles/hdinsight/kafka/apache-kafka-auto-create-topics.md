---
title: A témakör automatikus létrehozásának engedélyezése Apache Kafkaban – Azure HDInsight
description: Megtudhatja, hogyan konfigurálhat Apache Kafka a HDInsight-ben a témakörök automatikus létrehozásához. A Kafka konfigurálásához állítsa be az Auto. Create. témákat. engedélyezze a Ambari-n keresztül, vagy a fürt létrehozásakor a PowerShell vagy a Resource Manager-sablonok használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: 393087f4d5c5e7a52fd2dd10d20362a045a0075b
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122664"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>A HDInsight Apache Kafka konfigurálása a témakörök automatikus létrehozásához

Alapértelmezés szerint a [Apache Kafka](https://kafka.apache.org/) on HDInsight nem engedélyezi az automatikus témakör létrehozását. Az [Apache Ambari](https://ambari.apache.org/)használatával engedélyezheti az automatikus témakör létrehozását a meglévő fürtökhöz. Az új Kafka-fürt Azure Resource Manager sablon használatával történő létrehozásakor is engedélyezheti az automatikus témakör létrehozásának lehetőségeit.

## <a name="apache-ambari-web-ui"></a>Apache Ambari Web UI

Ha engedélyezni szeretné az automatikus témakör létrehozását egy meglévő fürtön a Ambari webes felhasználói felületén keresztül, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza ki a Kafka-fürtöt.

2. A __fürt áttekintése__lapon válassza a __fürt irányítópultja__lehetőséget.

    ![A portál képe, amelyen a fürt irányítópultja ki van választva](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Ezután válassza a __HDInsight-fürt irányítópultja__lehetőséget. Ha a rendszer kéri, végezzen hitelesítést a fürt bejelentkezési (rendszergazdai) hitelesítő adataival.

    ![A HDInsight-fürt irányítópult-bejegyzésének képe](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Válassza ki a Kafka szolgáltatást a lap bal oldalán található listából.

    ![Apache Ambari-szolgáltatás listája lap](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

4. Válassza a konfigurációk lehetőséget az oldal közepén.

    ![Apache Ambari szolgáltatás – konfigurációk lap](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

5. A szűrő mezőbe írja be a értékét `auto.create`.

    ![Apache Ambari keresési szűrő mező](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Ezzel kiszűri a tulajdonságok listáját, és `auto.create.topics.enable` megjeleníti a beállítást.

6. Módosítsa a értéket a `auto.create.topics.enable` értékre `true`, majd válassza a mentés lehetőséget. Vegyen fel egy megjegyzést, majd válassza a Mentés újra lehetőséget.

    ![Az Auto. Create. témák. Enable bejegyzés képe](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Válassza ki a Kafka szolgáltatást, válassza az __Újraindítás__lehetőséget, majd kattintson az __összes érintett újraindítása__elemre. Ha a rendszer kéri, válassza __az összes újraindításának megerősítése__lehetőséget.

    ![Az Apache Ambari újraindítása minden érintett](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> A Ambari értékeket a Ambari REST API is megadhatja. Ez általában nehezebb, mivel több REST-hívást kell megszereznie a jelenlegi konfiguráció beolvasásához, a módosítást stb. További információ: [HDInsight-fürtök kezelése az Apache Ambari REST API dokumentum használatával](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

Amikor egy Azure Resource Manager sablonnal hoz létre egy Kafka-fürtöt, közvetlenül is `auto.create.topics.enable` beállíthatja azt `kafka-broker`a alkalmazásban. A következő JSON-kódrészlet bemutatja, hogyan állíthatja be ezt az `true`értéket:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtudhatta, hogyan engedélyezheti az automatikus témakör létrehozását a HDInsight-Apache Kafka. Ha többet szeretne megtudni a Kafka használatáról, tekintse meg az alábbi hivatkozásokat:

* [Apache Kafka naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Az Apache Kafka-fürtök közötti adatreplikálás](apache-kafka-mirroring.md)
