---
title: Automatikus témakör-létrehozás engedélyezése az Apache Kafkában – Azure HDInsight
description: Ismerje meg, hogyan konfigurálhatja az Apache Kafka-t a HDInsight-on a témakörök automatikus létrehozásához. A Kafka konfigurálható az auto.create.topics.enable beállításával, hogy az Ambari-n keresztül igaz legyen, vagy a Fürt létrehozása során PowerShell vagy Erőforrás-kezelő sablonokon keresztül.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 7ec7d15806927306b12624962facbafddf2ce08b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242360"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Az Apache Kafka konfigurálása a HDInsight-on a témakörök automatikus létrehozásához

Alapértelmezés szerint az [Apache Kafka](https://kafka.apache.org/) a HDInsight-on nem engedélyezi az automatikus témakörlétrehozását. Az [Apache Ambari](https://ambari.apache.org/)segítségével engedélyezheti az automatikus témakészítést a meglévő fürtökhöz. Az automatikus témakörlétrehozása is engedélyezheti, ha egy új Kafka-fürt öt Azure Resource Manager-sablon használatával hoz létre.

## <a name="apache-ambari-web-ui"></a>Apache Ambari webes felhasználói felület

Ha engedélyezni szeretné az automatikus témakörlétrehozását egy meglévő fürtön az Ambari webfelhasználói felületen keresztül, kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)válassza ki a Kafka-fürt.

1. A **Fürtirányítópultok**területen válassza az **Ambari home**lehetőséget.

    ![A portál képe a kijelölt fürt-irányítópulttal](./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png)

    Amikor a rendszer kéri, hitelesítse magát a fürt bejelentkezési (rendszergazdai) hitelesítő adataival. Másik lehetőségként közvetlenül az Amabri-fürt nevéből csatlakozhat a Mabrihoz. `https://CLUSTERNAME.azurehdinsight.net/` `CLUSTERNAME`

1. Válassza ki a Kafka szolgáltatást a lap bal oldalán található listából.

    ![Apache Ambari szolgáltatáslista lap](./media/apache-kafka-auto-create-topics/hdinsight-service-list.png)

1. A lap közepén válassza a Konfigurációk elemet.

    ![Apache Ambari szolgáltatás konfigurációs lapja](./media/apache-kafka-auto-create-topics/hdinsight-service-config.png)

1. A Szűrő mezőbe írja be `auto.create`a értékét.

    ![Apache Ambari keresőszűrő mező](./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png)

    Ez szűri a tulajdonságok `auto.create.topics.enable` listáját, és megjeleníti a beállítást.

1. Módosítsa a `auto.create.topics.enable` értékét `true`a értékén, majd válassza a **Mentés**lehetőséget. Vegyen fel egy jegyzetet, majd kattintson ismét **a Mentés** gombra.

    ![Az auto.create.topics.enable bejegyzés képe](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

1. Válassza a Kafka szolgáltatást, válassza __az Újraindítás__lehetőséget, majd az Összes __érintett újraindítása__lehetőséget. Amikor a rendszer kéri, válassza __az Újraindítás megerősítése az összeset__lehetőséget.

    ![Az Apache Ambari minden érintett](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]  
> Ambari értékeket az Ambari REST API-n keresztül is beállíthat. Ez általában nehezebb, mivel több REST-hívást kell kezdeményeznie az aktuális konfiguráció lekéréséhez, módosításához stb. További információ: [HDInsight-fürtök kezelése az Apache Ambari REST API-dokumentum használatával.](../hdinsight-hadoop-manage-ambari-rest-api.md)

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

Ha Egy Kafka-fürtöt Azure Resource Manager-sablonnal hoz létre, közvetlenül beállíthatja, `auto.create.topics.enable` ha hozzáadja azt a. `kafka-broker` A következő JSON-kódrészlet bemutatja, hogyan `true`állíthatja be ezt az értéket:

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

Ebben a dokumentumban megtanulta, hogyan engedélyezheti az Automatikus témakörek létrehozását az Apache Kafka számára a HDInsighton. Ha többet szeretne megtudni a Kafka együttműködéséről, olvassa el az alábbi hivatkozásokat:

* [Apache Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Adatok replikálása Apache Kafka-fürtök között](apache-kafka-mirroring.md)
