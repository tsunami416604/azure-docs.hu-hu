---
title: Az Azure HDInsight-fürt megfelelő virtuálisgép-méretének kiválasztása
description: Ismerje meg, hogyan választhatja ki a hdinsight-fürt megfelelő virtuálisgép-méretét.
keywords: vm-méretek, fürtméretek, fürtkonfiguráció
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: a21e8d6c76c93b3084619c09f6a7664a25c1929c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682212"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Az Azure HDInsight-fürt megfelelő virtuálisgép-méretének kiválasztása

Ez a cikk ismerteti, hogyan válassza ki a megfelelő virtuális gép méretét a HDInsight-fürt különböző csomópontjaihoz. 

Először is, hogy megértsék, hogy a virtuális gép tulajdonságai, például a CPU-feldolgozás, a RAM-méret és a hálózati késés hatással lesz a számítási feladatok feldolgozását. Ezután gondolja át az alkalmazást, és hogyan egyezik meg azzal, amire a különböző virtuálisgép-családok vannak optimalizálva. Győződjön meg arról, hogy a használni kívánt virtuálisgép-család kompatibilis a telepíteni kívánt fürttípussal. Az egyes fürttípusokhoz támogatott és ajánlott virtuálisgép-méretek listáját az [Azure HDInsight által támogatott csomópontkonfigurációk című témakörben tetszetős.](hdinsight-supported-node-configuration.md) Végül egy teljesítményértékelési folyamat segítségével tesztelheti néhány minta számítási feladatok, és ellenőrizze, hogy melyik termékváltozat az adott családon belül az Ön számára megfelelő.

A fürt egyéb szempontjainak tervezésével, például a tárolótípus vagy a fürtméret kiválasztásával kapcsolatos további tudnivalókért olvassa el [a Kapacitástervezés hdinsight-fürtökhöz című témakört.](hdinsight-capacity-planning.md)

## <a name="vm-properties-and-big-data-workloads"></a>Virtuálisgép-tulajdonságok és big data-számítási feladatok

A virtuális gép méretét és típusát a PROCESSZOR feldolgozási teljesítménye, a RAM-méret és a hálózati késés határozza meg:

- CPU: A virtuális gép mérete határozza meg a magok számát. Minél több mag, annál nagyobb mértékű párhuzamos számítás minden csomópont képes elérni. Emellett egyes virtuálisgép-típusok gyorsabb magokkal rendelkeznek.

- RAM: A virtuális gép mérete is diktálja a virtuális gépben elérhető RAM mennyiségét. A számítási feladatok, amelyek az adatokat a memóriában a feldolgozás, ahelyett, hogy a lemezről olvasás, győződjön meg arról, hogy a munkavégző csomópontok elegendő memóriával rendelkezik, hogy illeszkedjen az adatokhoz.

- Hálózat: A legtöbb fürttípus esetében a fürt által feldolgozott adatok nem a helyi lemezen vannak, hanem egy külső tárolási szolgáltatásban, például a Data Lake Storage-ban vagy az Azure Storage-ban. Vegye figyelembe a hálózati sávszélesség és átviteli a csomópont virtuális gép és a tárolási szolgáltatás között. A virtuális gép számára elérhető hálózati sávszélesség általában nagyobb méretben növekszik. További részletek: [Virtuálisgép-méretek – áttekintés.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)

## <a name="understanding-vm-optimization"></a>A virtuális gépek optimalizálásának ismertetése

Az Azure virtuálisgép-családok a különböző használati esetekre vannak optimalizálva. Az alábbi táblázatban található néhány, a legnépszerűbb használati esetek és a virtuális gép családok, amelyek megfelelnek nekik.

| Típus                     | Méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Belépés](../virtual-machines/linux/sizes-general.md)          | A, Av2  | A processzorteljesítmény és a memóriakonfigurációk a legalkalmasabbak a belépő szintű számítási feladatokhoz, például a fejlesztéshez és a teszteléshez. Gazdaságosak, és alacsony költségű lehetőséget biztosítanak az Azure-ral való ismerkedéshez. |
| [Általános célú](../virtual-machines/linux/sizes-general.md)          | D, DSv2, Dv2  | Kiegyensúlyozott CPU-memória arány. Ideális választások a teszteléshez és a fejlesztéshez, a kicsi és közepes adatbázisokhoz, illetve az alacsony és közepes forgalmú webkiszolgálókhoz. |
| [Számításra optimalizált](../virtual-machines/linux/sizes-compute.md)        | F           | Magas CPU-memória arány. Jó közepes forgalmú webkiszolgálókhoz, hálózati készülékekhez, kötegelt folyamatokhoz és alkalmazáskiszolgálókhoz.        |
| [Memóriaoptimalizált](../virtual-machines/linux/sizes-memory.md)         | Esv3, Ev3  | Magas memória-CPU arány. Ideális választások a relációs adatbázisok kiszolgálóihoz, a közepes és nagy gyorsítótárakhoz, valamint a memóriában végzett elemzésekhez.                 |

- A HDInsight által támogatott régiókban elérhető virtuálisgép-példányok díjszabásáról a [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/)című témakörben talál további információt.

## <a name="cost-saving-vm-types-for-light-workloads"></a>Költségtakarékos virtuálisgép-típusok könnyű munkaterheléshez

Ha rendelkezik a könnyű feldolgozási követelményekkel, az [F sorozat](https://azure.microsoft.com/blog/f-series-vm-size/) jó választás lehet a HDInsight megkezdéséhez. Az alacsonyabb óránkénti listaáron elérhető F-sorozat képviseli a legjobb ár–teljesítmény arányt az Azure-portfólióban az egy vCPU-ra jutó Azure-alapú számítási egységek (Azure Compute Unit, ACU) alapján.

Az alábbi táblázat az Fsv2 sorozatú virtuális gépeken létrehozható fürttípusokat és csomóponttípusokat ismerteti.

| Fürt típusa | Verzió | Dolgozói csomópont | Fő csomópont | Zookeeper csomópont |
|---|---|---|---|---|
| Spark | Összes | F4 és újabb | nem | nem |
| Hadoop | Összes | F4 és újabb | nem | nem |
| Kafka | Összes | F4 és újabb | nem | nem |
| HBase | Összes | F4 és újabb | nem | nem |
| LLAP | Tiltva | nem | nem | nem |
| Storm | Tiltva | nem | nem | nem |
| ML szolgáltatás | CSAK HDI 3.6 | F4 és újabb | nem | nem |

Az egyes F sorozatú termékváltozatok specifikációit az [F sorozatú virtuális gépek méretei című témakörben tetszésszerint olvassa](https://azure.microsoft.com/blog/f-series-vm-size/)el.

## <a name="benchmarking"></a>Teljesítményértékelés

Az összehasonlítás a szimulált számítási feladatok különböző virtuális gépeken való futtatásának folyamata annak mérésére, hogy milyen jól fognak működni az éles számítási feladatokhoz. 

A virtuális gép sk-ek és a fürtméretek teljesítményértékeléséről további információt a [Fürtkapacitás-tervezés az Azure HDInsightban című témakörben talál. ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type)

## <a name="next-steps"></a>További lépések

- [Az Azure HDInsight támogatja a csomópontkonfigurációkat](hdinsight-supported-node-configuration.md)
- [A Linux rendszerű virtuális gépek méretei az Azure-ban](../virtual-machines/linux/sizes.md)
