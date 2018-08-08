---
title: Az operációs rendszer javításai a Linux-alapú HDInsight-fürtök – Azure ütemezésének konfigurálása
description: Ismerje meg az operációs rendszer javításai a Linux-alapú HDInsight-fürtök ütemezés konfigurálása.
services: hdinsight
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 5d8fae7412bece631e6961fb92b7d87e308cc857
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599214"
---
# <a name="os-patching-for-hdinsight"></a>Operációs rendszer javításai a HDInsight 
Felügyelt Hadoop-szolgáltatás HDInsight gondoskodik az operációs rendszer, a HDInsight-fürtök által használt szoftvert futtató virtuális gépek javítása. 2016. augusztus 1-től módosítottuk a vendég operációs rendszer javítási házirend Linux-alapú HDInsight-fürtök (3.4-es vagy újabb verzió). Az új házirend az a célja, hogy jelentősen csökkentheti a javítás miatt újraindítások száma. Javítás virtuális gépeken (VM) az új házirend továbbra Linux-fürtökön minden hétfőn vagy csütörtök 12 AM UTC lépcsőzetes módon díjtól bármely adott fürtben található csomópontok között. Azonban bármely adott virtuális gép csak újraindítás legfeljebb 30 naponta miatt a vendég operációs rendszer javításait. Emellett az újonnan létrehozott fürt első újraindítás nem történik meg hamarabb fürt létrehozás dátumától számítva 30 napnál. Javítások től lép érvénybe, ha a virtuális gépek indulnak újra.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Az operációs rendszer javítási ütemezése a Linux-alapú HDInsight-fürtök konfigurálása
Egy HDInsight-fürtön lévő virtuális gépek kell alkalmanként indítani, hogy fontos biztonsági javításokat is telepíthető. 2016. augusztus 1. új Linux-alapú HDInsight-fürtök (3.4-es vagy újabb verzió) újraindulásakor az alábbi ütemezés:

1. A fürt egy virtuális gépet is csak indítsa újra a javítások legfeljebb egyszer egy 30 napos időtartamon belül.
2. Az újraindítás megtörténik induló 12 AM (UTC).
3. Az újraindítás folyamatban van egyenletesen elosztani a fürtben lévő virtuális gépek között, ezért az újraindítás során a fürt továbbra is elérhető.
4. Az újonnan létrehozott fürt első újraindítás nem történik meg a fürt létrehozást követően 30 napnál hamarabb.

A jelen cikkben ismertetett szkriptműveletek használatával, módosíthatja az operációs rendszer következő javítási ütemezése:
1. Engedélyezi vagy letiltja az automatikus újraindítások
2. Set gyakorisága (napokban újraindításnál) újraindul
3. Ha újraindítás történik a hét napját beállítása

> [!NOTE]
> Ez a parancsprogram-művelet csak a 2016. augusztus 1. után létrehozott Linux-alapú HDInsight-fürtökkel működik. Javítások től lép érvénybe, csak akkor, ha a virtuális gépek indulnak újra. 
>

## <a name="how-to-use-the-script"></a>A parancsfájl használata 

Ha ez a szkript használatához a következő információkat:
1. A parancsprogram helye: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  HDInsight ezt az URI használatával keresse meg és futtassa a parancsfájlt a fürt összes virtuális gépet.
  
2. A alkalmazni a parancsfájl fürtcsomópont-típusokat: átjárócsomópontjával, workernode, zookeeper. Ez a szkript a fürt minden csomópont típusokat kell alkalmazni. Ez a csomópont típusa nem alkalmazható, ha a virtuális gépek adott csomóponttípus továbbra korábbi javítási ütemezésének használja.


3.  Paraméter: Ez a szkript numerikus három paramétert fogad el:

    | Paraméter | Meghatározás |
    | --- | --- |
    | Engedélyezi/letiltja az automatikus újraindítások |0 vagy 1. A 0 érték letiltja az automatikus újraindítások 1: engedélyezi az automatikus újraindítások során. |
    | Gyakoriság |7 – 90 (a szélsőértékek megengedettek). Hány nap után újraindul a javításokat az újraindítást igénylő virtuális gépek száma. |
    | Hét napja |1 és 7 (a szélsőértékek megengedettek). Az 1 érték azt jelzi, az újraindítás megtörténik, hétfőn, és 7 azt jelzi, hogy egy Sunday.For paraméterek használatával a példában 1 60 2 eredmények automatikus újraindul 60 naponta (legfeljebb) keddjén. |
    | Adatmegőrzés |Egy meglévő fürthöz szkriptműveletet alkalmazásakor, megőrzött meg lehet jelölni a parancsfájl. A megőrzött szkriptek akkor lépnek fel, amikor új workernodes hozzáadódnak a fürtnek a méretezési műveletek. |

> [!NOTE]
> Ez a szkript, egy meglévő fürthöz alkalmazásakor megőrzött kell megjelölni. Ellenkező esetben a méretezési műveletek során létrehozott minden új csomópontok fogja használni az alapértelmezett javítási ütemezése.
Ha a parancsfájl a Fürtlétrehozási folyamat részeként, állandó automatikusan.
>

## <a name="next-steps"></a>További lépések

A script actionnel részletes lépéseit lásd a következő szakaszai a [testreszabása Linuz-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md):

* [Fürt létrehozása során egy parancsfájlművelettel](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Egy futó fürt szkriptműveletet vonatkozik](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
