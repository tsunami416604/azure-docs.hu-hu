---
title: "Magas rendelkezésre állás az Apache Kafka platformmal – Azure HDInsight | Microsoft Docs"
description: "Útmutató magas rendelkezésre állás biztosításához az Apache Kafka platformmal az Azure HDInsight szolgáltatásban. Megtudhatja, hogyan egyensúlyozza ki újra a partíciók replikáit a Kafkában, hogy különböző tartalék tartományban legyenek a HDInsightot tartalmazó Azure régióban."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/20/2017
ms.author: larryfr
ms.openlocfilehash: 3edec2e68356604562af2219ccd498732c564ec5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-of-your-data-with-apache-kafka-preview-on-hdinsight"></a>Magas rendelkezésre állású adatok a HDInsightban az Apache Kafka platformmal (előzetes verzió)

Útmutató partícióreplikák Kafka-témakörökhöz való konfigurálásához az alapul szolgáló hardverállvány-konfiguráció előnyeinek kihasználásához. Ez a konfiguráció biztosítja a HDInsighton az Apache Kafka platformon tárolt adatok rendelkezésre állását.

## <a name="fault-and-update-domains-with-kafka"></a>Tartalék és frissítési tartományok a Kafka platformmal

A tartalék tartomány az alapul szolgáló hardver logikai csoportosítása egy Azure-adatközpontban. Mindegyik tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkezik. A HDInsight-fürtön belül a csomópontokat implementáló virtuális gépek és felügyelt lemezek ezek között a tartalék tartományok között vannak elosztva. Ez az architektúra csökkenti a fizikai hardverhibák lehetséges hatását.

Mindegyik Azure-régió meghatározott számú tartalék tartománnyal rendelkezik. A tartományok listáját és a bennük található tartalék tartományok számát a [Rendelkezésre állási készletek](../virtual-machines/linux/regions-and-availability.md#availability-sets) dokumentációjában találja.

> [!IMPORTANT]
> A Kafka nem kezeli a tartalék tartományokat. Amikor létrehoz egy témakört a Kafkában, az lehet hogy minden partícióreplikát ugyanabban a tartalék tartományban tárol. Ennek a problémának a megoldásához biztosítjuk a [Kafka partíció-újraegyensúlyozó eszközt](https://github.com/hdinsight/hdinsight-kafka-tools).

## <a name="when-to-rebalance-partition-replicas"></a>Mikor van szükség a partícióreplikák újraegyensúlyozására?

A Kafka-adatok lehető legmagasabb rendelkezésre állásának biztosításához a következő időpontokban kell újra egyensúlyoznia a partícióreplikákat a témaköréhez:

* Új témakör vagy partíció létrehozásakor

* Fürt vertikális felskálázásakor

## <a name="replication-factor"></a>Replikációs tényező

> [!IMPORTANT]
> Javasoljuk, hogy olyan Azure-régiót használjon, amely három tartalék tartományt tartalmaz, és használjon 3-as replikációs tényezőt.

Ha kénytelen olyan régiót használni, amely csak két tartalék tartomány tartalmaz, használjon 4-es replikációs tényezőt, hogy egyenletesen ossza el a replikákat a két tartalék tartományban.

A témakörök létrehozására és a replikációs tényező beállítására példát a [Kezdő lépések a Kafkával a HDInsightban](hdinsight-apache-kafka-get-started.md) dokumentumban talál.

## <a name="how-to-rebalance-partition-replicas"></a>A partícióreplikák újraegyensúlyozása

A kiválasztott témakörök újraegyensúlyozására használja a [Kafka partíció-újraegyensúlyozó eszközt](https://github.com/hdinsight/hdinsight-kafka-tools). Ezt az eszközt egy SSH-munkamenetből kell futtatni a Kafka-fürt főcsomópontjához.

A HDInsight-hoz SSH-val való kapcsolódásról további információért lásd az [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md) dokumentumot.

## <a name="next-steps"></a>Következő lépések

* [A Kafka méretezhetősége a HDInsighton](hdinsight-apache-kafka-scalability.md)
* [Tükrözés Kafkával a HDInsighton](hdinsight-apache-kafka-mirroring.md)