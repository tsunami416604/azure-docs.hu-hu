---
title: Az Apache Kafka - Azure HDInsight automatikus témakör létrehozásának engedélyezése |} Microsoft Docs
description: Útmutató Apache Kafka konfigurálása a HDInsight témakörök automatikus létrehozásához. Konfigurálhatja a Kafka auto.create.topics.enable Ambari vagy PowerShell vagy az erőforrás-kezelő sablonok révén a fürt létrehozása során true értékre állításával.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2018
ms.author: larryfr
ms.openlocfilehash: fa5dd7533259c794671cd16231fd3f530173bfa3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>A HDInsight automatikusan létrehozza a témakörök Apache Kafka konfigurálása

Alapértelmezés szerint a HDInsight Kafka nem engedélyezi az automatikus témakör létrehozása. Automatikus témakör létrehozása meglévő fürtök Ambari használatával engedélyezheti. Engedélyezheti az automatikus témakör létrehozása, amikor egy új Azure Resource Manager-sablonnal Kafka fürtöt hoz létre.

## <a name="ambari-web-ui"></a>Ambari webes felhasználói felület

Ahhoz, hogy egy meglévő fürt az Ambari webes felhasználói felületén keresztül automatikus témakör létrehozását, tegye a következőket:

1. Az a [Azure-portálon](https://portal.azure.com), válassza ki a Kafka fürtöt.

2. Az a __fürtök – áttekintés__, jelölje be __fürt irányítópult__. 

    ![A portálhoz a kijelölt fürt irányítópult képe](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Válassza ki __HDInsight fürt irányítópult__. Amikor a rendszer kéri, hitelesíteni a fürt (rendszergazda) bejelentkezési hitelesítő adataival.

    ![A HDInsight-fürt irányítópult bejegyzés képe](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Válassza ki a Kafka szolgáltatást a lap bal oldalon a listából.

    ![Szolgáltatás listája](./media/apache-kafka-auto-create-topics/service-list.png)

4. Válassza ki a konfigurációk a lap közepén.

    ![Szolgáltatás-konfiguráció lapon](./media/apache-kafka-auto-create-topics/service-config.png)

5. A Szűrő mezőbe írja be az érték `auto.create`. 

    ![A Szűrő mezőbe képe](./media/apache-kafka-auto-create-topics/filter.png)

    A szűrés a tulajdonságlista és megjeleníti a `auto.create.topics.enable` beállítást.

6. Módosítsa az értéket a `auto.create.topics.enable` való `true`, és válassza a mentés. Megjegyzés hozzáadása, és válassza mentse újból.

    ![A auto.create.topics.enable bejegyzés képe](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Válassza ki a Kafka szolgáltatást, jelölje be __indítsa újra a__, majd válassza ki __indítsa újra az összes érintett__. Amikor a rendszer kéri, válassza ki a __megerősítése indítsa újra az összes__.

    ![Újraindítás kijelölés képe](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]
> Az Ambari REST API-n keresztül Ambari értékeket állíthat be. Ez nehezebb általában, ha van több REST hívásokat kéri le a jelenlegi konfigurációt, módosítsa azt, stb. További információkért lásd: a [kezelése HDInsight-fürtök az Ambari REST API használatával](../hdinsight-hadoop-manage-ambari-rest-api.md) dokumentum.

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

Ha Azure Resource Manager-sablonnal Kafka fürt létrehozása, közvetlenül beállíthatja `auto.create.topics.enable` hozzáadva a egy `kafka-broker`. A következő JSON-részlet mutatja be az érték `true`:

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

Ebben a dokumentumban megtanulhatta Kafka a HDInsight a témakör az automatikus létrehozásának engedélyezéséhez. Kafka kezelésével kapcsolatos további tudnivalókért tekintse meg a következőket:

* [Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Adatreplikálás Kafka-fürtök között](apache-kafka-mirroring.md)