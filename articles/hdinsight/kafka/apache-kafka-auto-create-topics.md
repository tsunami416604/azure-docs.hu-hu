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
ms.openlocfilehash: 65dd4b172c0913e11bb7f25909eb062e93a4a03a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51015230"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Az Apache Kafka HDInsight automatikusan létrehozza a témakörök konfigurálása

Alapértelmezés szerint a HDInsight alatt futó Kafka nem engedélyezi az automatikus témakör-létrehozás. Automatikus témakör-létrehozás már meglévő fürtök az Ambari használatával engedélyezheti. Automatikus témakör-létrehozás egy új Azure Resource Manager-sablon használatával Kafka-fürt létrehozásakor is engedélyezheti.

## <a name="ambari-web-ui"></a>Az Ambari webes felhasználói felületen

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
> Ambari-értékek az Ambari REST API-n keresztül is beállíthatja. Ez a lehetőség általában sokkal nehezebb, hogy több REST-hívást a jelenlegi konfiguráció lekérése, módosítsa azt, és így tovább. További információkért lásd: a [kezelése a HDInsight-fürtök az Ambari REST API használatával](../hdinsight-hadoop-manage-ambari-rest-api.md) dokumentumot.

## <a name="resource-manager-templates"></a>Erőforrás-kezelői sablonok

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

Ebben a dokumentumban megtudhatta, hogyan ahhoz, hogy a HDInsight alatt futó Kafka az automatikus témakör létrehozása. A Kafka használata kapcsolatos további információkért tekintse meg az alábbi hivatkozásokat:

* [Kafka-naplók elemzése](apache-kafka-log-analytics-operations-management.md)
* [Adatreplikálás Kafka-fürtök között](apache-kafka-mirroring.md)
