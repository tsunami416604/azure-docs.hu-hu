---
title: A témakör automatikus létrehozásának engedélyezése Apache Kafkaban – Azure HDInsight
description: Megtudhatja, hogyan konfigurálhat Apache Kafka a HDInsight-ben a témakörök automatikus létrehozásához. A Kafka konfigurálásához állítsa be az Auto. Create. témákat. engedélyezze a Ambari-n keresztül, vagy a fürt létrehozásakor a PowerShell vagy a Resource Manager-sablonok használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 1bcb433230af856e92c7e418fc81b35bea549ddf
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72987971"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>A HDInsight Apache Kafka konfigurálása a témakörök automatikus létrehozásához

Alapértelmezés szerint a HDInsight [Apache Kafka](https://kafka.apache.org/) nem engedélyezi az automatikus témakör létrehozását. Az [Apache Ambari](https://ambari.apache.org/)használatával engedélyezheti az automatikus témakör létrehozását a meglévő fürtökhöz. Az új Kafka-fürt Azure Resource Manager sablon használatával történő létrehozásakor is engedélyezheti az automatikus témakör létrehozásának lehetőségeit.

## <a name="apache-ambari-web-ui"></a>Apache Ambari webes felhasználói felület

Ha engedélyezni szeretné az automatikus témakör létrehozását egy meglévő fürtön a Ambari webes felhasználói felületén keresztül, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza ki a Kafka-fürtöt.

1. A **fürt irányítópultok**területén válassza a **Ambari Kezdőlap**lehetőséget.

    ![A portál képe, amelyen a fürt irányítópultja ki van választva](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Ha a rendszer kéri, végezzen hitelesítést a fürt bejelentkezési (rendszergazdai) hitelesítő adataival. Azt is megteheti, hogy közvetlenül a `https://CLUSTERNAME.azurehdinsight.net/` csatlakozhat a Amabri, ahol `CLUSTERNAME` a Kafka-fürt neve.

1. Válassza ki a Kafka szolgáltatást a lap bal oldalán található listából.

    ![Apache Ambari-szolgáltatás listája lap](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. Válassza a konfigurációk lehetőséget az oldal közepén.

    ![Apache Ambari szolgáltatás – konfigurációk lap](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. A szűrő mezőbe írja be a `auto.create`értékét.

    ![Apache Ambari keresési szűrő mező](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Ez szűri a tulajdonságok listáját, és megjeleníti a `auto.create.topics.enable` beállítást.

1. Módosítsa `auto.create.topics.enable` értékét `true`re, majd válassza a **Mentés**lehetőséget. Vegyen fel egy megjegyzést, majd válassza a **Mentés** újra lehetőséget.

    ![Az Auto. Create. témák. Enable bejegyzés képe](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Válassza ki a Kafka szolgáltatást, válassza az __Újraindítás__lehetőséget, majd kattintson az __összes érintett újraindítása__elemre. Ha a rendszer kéri, válassza __az összes újraindításának megerősítése__lehetőséget.

    ![Az Apache Ambari újraindítása minden érintett](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> A Ambari értékeket a Ambari REST API is megadhatja. Ez általában nehezebb, mivel több REST-hívást kell megszereznie a jelenlegi konfiguráció beolvasásához, a módosítást stb. További információ: [HDInsight-fürtök kezelése az Apache Ambari REST API dokumentum használatával](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Erőforrás-kezelői sablonok

Amikor egy Azure Resource Manager sablonnal hoz létre egy Kafka-fürtöt, közvetlenül is beállíthatja `auto.create.topics.enable` úgy, hogy hozzáad egy `kafka-broker`. A következő JSON-kódrészlet bemutatja, hogyan állíthatja be ezt az értéket `true`re:

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

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan engedélyezheti az automatikus témakör létrehozását a HDInsight-Apache Kafka. Ha többet szeretne megtudni a Kafka használatáról, tekintse meg az alábbi hivatkozásokat:

* [Apache Kafka naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Az Apache Kafka-fürtök közötti adatreplikálás](apache-kafka-mirroring.md)
