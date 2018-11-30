---
title: Az Apache kafka platformmal – Azure HDInsight automatikus témakör-létrehozás engedélyezése
description: Ismerje meg, hogyan konfigurálhatja az Apache Kafka HDInsight automatikusan létrehozza a témakörök. A Kafka konfigurálhatja úgy, hogy igaz ambarival, vagy a PowerShell vagy a Resource Manager-sablonokkal a fürt létrehozásakor auto.create.topics.enable.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: e78f0d2012cf4f6152e69fff1bab04f827c0eef7
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310888"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Az Apache Kafka HDInsight automatikusan létrehozza a témakörök konfigurálása

Alapértelmezés szerint [Apache Kafka](https://kafka.apache.org/) a HDInsight nem engedélyezi az automatikus témakör-létrehozás. Engedélyezheti az automatikus témakör-létrehozás már meglévő fürtök [Apache Ambari](https://ambari.apache.org/). Automatikus témakör-létrehozás egy új Azure Resource Manager-sablon használatával Kafka-fürt létrehozásakor is engedélyezheti.

## <a name="apache-ambari-web-ui"></a>Az Apache Ambari webes Felülettel

Az Ambari webes felhasználói felületen keresztül meglévő fürt automatikus témakör-létrehozás engedélyezése, használja az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com), válassza ki a Kafka-fürt.

2. Az a __fürt áttekintés__válassza __fürt irányítópultja__. 

    ![A portálon a kijelölt fürt-irányítópult képe](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Válassza ki __HDInsight-fürt irányítópultja__. Amikor a rendszer kéri, hitelesíteni a fürt bejelentkezési (rendszergazdai) hitelesítő adataival.

    ![A HDInsight-fürt irányítópult bejegyzést ábrázoló kép](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Válassza ki a Kafka szolgáltatás az oldal bal oldalán lévő listából.

    ![Lista](./media/apache-kafka-auto-create-topics/service-list.png)

4. Válassza ki a konfigurációkat az oldal közepén.

    ![Szolgáltatás konfigurálása lap](./media/apache-kafka-auto-create-topics/service-config.png)

5. A Szűrő mezőbe írja be a egy értéke `auto.create`. 

    ![A Szűrő mezőbe képe](./media/apache-kafka-auto-create-topics/filter.png)

    Ez a tulajdonságokat, és megjeleníti listájának szűrése a `auto.create.topics.enable` beállítás.

6. Módosítsa az értéket a `auto.create.topics.enable` való `true`, majd válassza a Mentés gombra. Megjegyzés hozzáadása, majd mentse újból.

    ![A auto.create.topics.enable bejegyzést ábrázoló kép](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Válassza ki a Kafka szolgáltatás kiválasztása __indítsa újra a__, majd válassza ki __indítsa újra az összes érintett__. Amikor a rendszer kéri, válassza ki a __megerősítése indítsa újra az összes__.

    ![Újraindítás kijelölés képe](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]
> Ambari-értékek az Ambari REST API-n keresztül is beállíthatja. Ez a lehetőség általában sokkal nehezebb, hogy több REST-hívást a jelenlegi konfiguráció lekérése, módosítsa azt, és így tovább. További információkért lásd: a [kezelése a HDInsight-fürtök az Apache Ambari REST API használatával](../hdinsight-hadoop-manage-ambari-rest-api.md) dokumentumot.

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

Egy Azure Resource Manager-sablon használatával a Kafka-fürt létrehozásakor is közvetlenül megadhatja a `auto.create.topics.enable` hozzáadásával a egy `kafka-broker`. Az alábbi JSON-kódrészlet bemutatja, hogyan lehet ez az érték `true`:

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

Ebben a dokumentumban megtudhatta, hogyan automatikus témakör létrehozását a HDInsight-beli Apache kafka lehetővé. A Kafka használata kapcsolatos további információkért tekintse meg az alábbi hivatkozásokat:

* [Az Apache Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Adatok replikálása Apache Kafka-fürtök között](apache-kafka-mirroring.md)
