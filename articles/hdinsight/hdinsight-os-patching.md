---
title: Az operációs rendszer javításai a Linux-alapú HDInsight-fürtök – Azure ütemezésének konfigurálása
description: Ismerje meg az operációs rendszer javításai a Linux-alapú HDInsight-fürtök ütemezés konfigurálása.
services: hdinsight
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: 402a4d59b57803b8a9c0094799ceee6a92df43f9
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911353"
---
# <a name="os-patching-for-hdinsight"></a>Operációs rendszer javításai a HDInsight 

> [!IMPORTANT]
> Ubuntu-rendszerképek válnak elérhetővé az új HDInsight-fürt létrehozása közzétehető 3 hónapon belül. Január a 2019-tól futó fürtök vannak **nem** automatikus tudjon fókuszálni. Ügyfeleink a futó fürt javítására irányuló szkriptműveletek vagy más mechanizmusok kell használnia.

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

## <a name="how-to-use-the-script"></a>A parancsfájl használata 

Ha ez a szkript használatához a következő információkat:
1. A parancsprogram helye: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  HDInsight ezt az URI használatával keresse meg és futtassa a parancsfájlt a fürt összes virtuális gépet.
  
2. A alkalmazni a parancsfájl fürtcsomópont-típusokat: átjárócsomópontjával, workernode, zookeeper. Ez a szkript a fürt minden csomópont típusokat kell alkalmazni. Ez a csomópont típusa nem alkalmazható, ha a virtuális gépek adott csomóponttípus továbbra korábbi javítási ütemezésének használja.


3.  A paraméter: Ez a szkript numerikus három paramétert fogad el:

    | Paraméter | Meghatározás |
    | --- | --- |
    | Engedélyezi/letiltja az automatikus újraindítások |0 vagy 1. A 0 érték letiltja az automatikus újraindítások 1: engedélyezi az automatikus újraindítások során. |
    | Gyakoriság |7 – 90 (a szélsőértékek megengedettek). Hány nap után újraindul a javításokat az újraindítást igénylő virtuális gépek száma. |
    | Hét napja |1 és 7 (a szélsőértékek megengedettek). Az 1 érték azt jelzi, az újraindítás megtörténik, hétfőn, és 7 azt jelzi, hogy egy Sunday.For paraméterek használatával a példában 1 60 2 eredmények automatikus újraindul 60 naponta (legfeljebb) keddjén. |
    | Adatmegőrzés |Egy meglévő fürthöz szkriptműveletet alkalmazásakor, megőrzött meg lehet jelölni a parancsfájl. A megőrzött szkriptek akkor lépnek fel, amikor új workernodes hozzáadódnak a fürtnek a méretezési műveletek. |

> [!NOTE]  
> Ez a szkript, egy meglévő fürthöz alkalmazásakor megőrzött kell megjelölni. Ellenkező esetben a méretezési műveletek során létrehozott minden új csomópontok fogja használni az alapértelmezett javítási ütemezése.  Ha a parancsfájl a Fürtlétrehozási folyamat részeként, állandó automatikusan.

## <a name="next-steps"></a>További lépések

A script actionnel részletes lépéseit lásd a következő szakaszai a [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md):

* [Fürt létrehozása során egy parancsfájlművelettel](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Egy futó fürt szkriptműveletet vonatkozik](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
