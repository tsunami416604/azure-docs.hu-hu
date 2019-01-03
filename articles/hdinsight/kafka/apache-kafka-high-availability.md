---
title: Magas rendelkezésre állás az Apache kafka platformmal – Azure HDInsight
description: Útmutató magas rendelkezésre állás biztosításához az Apache Kafka platformmal az Azure HDInsight szolgáltatásban. Megtudhatja, hogyan egyensúlyozza ki újra a partíciók replikáit a Kafkában, hogy különböző tartalék tartományban legyenek a HDInsightot tartalmazó Azure régióban.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2018
ms.openlocfilehash: 3d048618fef04b630366b3f575e420b329f102cb
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597664"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Magas rendelkezésre állású adatok a HDInsightban futó Apache Kafka platformmal

Megtudhatja, hogyan konfigurálhatja az alapul szolgáló hardverállvány-konfiguráció előnyeinek kihasználása érdekében az Apache Kafka-témakörök számára történő partícióreplikák. Ez a konfiguráció biztosítja a HDInsighton az Apache Kafka platformon tárolt adatok rendelkezésre állását.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Az Apache kafka platformmal tartalék és frissítési tartományok

A tartalék tartomány az alapul szolgáló hardver logikai csoportosítása egy Azure-adatközpontban. Mindegyik tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkezik. A HDInsight-fürtön belül a csomópontokat implementáló virtuális gépek és felügyelt lemezek ezek között a tartalék tartományok között vannak elosztva. Ez az architektúra csökkenti a fizikai hardverhibák lehetséges hatását.

Mindegyik Azure-régió meghatározott számú tartalék tartománnyal rendelkezik. A tartományok listáját és a bennük található tartalék tartományok számát a [Rendelkezésre állási készletek](../../virtual-machines/windows/regions-and-availability.md#availability-sets) dokumentációjában találja.

> [!IMPORTANT]  
> A Kafka nem kezeli a tartalék tartományokat. Amikor létrehoz egy témakört a Kafkában, az lehet hogy minden partícióreplikát ugyanabban a tartalék tartományban tárol. Ennek a problémának a megoldásához a HDInsight a [Kafka partíció-újraegyensúlyozó eszközt](https://github.com/hdinsight/hdinsight-kafka-tools) biztosítja.

## <a name="when-to-rebalance-partition-replicas"></a>Mikor van szükség a partícióreplikák újraegyensúlyozására?

A Kafka-adatok lehető legmagasabb rendelkezésre állásának biztosításához a következő időpontokban kell újra egyensúlyoznia a partícióreplikákat a témaköréhez:

* Új témakör vagy partíció létrehozásakor

* Fürt vertikális felskálázásakor

## <a name="replication-factor"></a>Replikációs tényező

> [!IMPORTANT]  
> Javasoljuk, hogy olyan Azure-régiót használjon, amely három tartalék tartományt tartalmaz, és használjon 3-as replikációs tényezőt.

Ha kénytelen olyan régiót használni, amely csak két tartalék tartomány tartalmaz, használjon 4-es replikációs tényezőt, hogy egyenletesen ossza el a replikákat a két tartalék tartományban.

Tekintse meg a témakörök létrehozására és a replikációs tényező beállítására példát a [a HDInsight Apache Kafka használatának első lépései](apache-kafka-get-started.md) dokumentumot.

## <a name="how-to-rebalance-partition-replicas"></a>A partícióreplikák újraegyensúlyozása

Használja a [Apache Kafka partíció-újraegyensúlyozó eszközt](https://github.com/hdinsight/hdinsight-kafka-tools) kiválasztott témakörök újraegyensúlyozására. Ezt az eszközt egy SSH-munkamenetből kell futtatni a Kafka-fürt főcsomópontjához.

A HDInsight-hoz SSH-val való kapcsolódásról további információért lásd az [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md) dokumentumot.

## <a name="next-steps"></a>További lépések

* [Az Apache Kafka on HDInsight méretezhetőség](apache-kafka-scalability.md)
* [Az Apache Kafka on HDInsight-tükrözés](apache-kafka-mirroring.md)
