---
title: "Az operációs rendszer javítását a Linux-alapú HDInsight-fürtök - Azure ütemezésének konfigurálása |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálja az operációs rendszer javítását a Linux-alapú HDInsight-fürtök ütemezését."
services: hdinsight
documentationcenter: 
author: bprakash
manager: asadk
editor: bprakash
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/21/2017
ms.author: bhanupr
ms.openlocfilehash: af3c5a19ae8e2e606e4b0506f9f6dddb41192e40
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="os-patching-for-hdinsight"></a>Az operációs rendszer a HDInsight javítását 
A Hadoop felügyelt szolgáltatásként HDInsight gondoskodik az operációs rendszer alapjául szolgáló virtuális gépek a HDInsight-fürtök által használt javítását. 2016. augusztus 1. a vendég operációs rendszer javítási házirend Linux-alapú HDInsight-fürtök (3.4 vagy újabb verziója) módosítottuk. Az új házirend célja jelentősen csökkentheti a javítás miatt újraindítások számát. Az új házirend minden hétfőn vagy bármely adott fürt csomópontjai között 12 óra UTC lépcsőzetes módon kezdődő csütörtök Linux fürtökön továbbra is javítás virtuális gépek (VM). A megadott virtuális gép azonban csak újraindul, legfeljebb 30 naponta egyszer miatt a vendég operációs rendszer javítását. Továbbá az első újraindítás, újonnan létrehozott fürt nem történik meg gyakoribb 30 napon a fürt létrehozását. Javítások hatékony lesz, ha a virtuális gép újraindítása van.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Az operációs rendszer javítását a Linux-alapú HDInsight-fürtök ütemezésének konfigurálása
A virtuális gépek a HDInsight-fürtök kell újra kell indítani alkalmanként, hogy fontos biztonsági javításokat is telepíthető. 2016 augusztusától 1-től új Linux-alapú HDInsight-fürtök (3.4 vagy újabb verzió) újraindítása van, használja a következő ütemezés szerint:

1. A virtuális gép a fürtben is csak a újra javítások legfeljebb egyszer egy 30 napos időtartamon belül.
2. Az újraindítás 12 óra UTC induló következik be.
3. A rendszer újraindítása folyamatban van lépcsőzetes elrendezésben a virtuális gépek a fürt között, ezért a rendszer újraindítása közben a fürt továbbra is elérhető.
4. Az újonnan létrehozott fürt első újraindítás nem történik meg hamarabb számított 30 napon belül a fürt létrehozásának dátumát.

A jelen cikkben ismertetett parancsfájlművelet használ, módosíthatja az operációs rendszer ütemezés javításokat az alábbiak szerint:
1. Engedélyezi vagy letiltja az automatikus újraindulását
2. Set gyakoriságát újraindul (újraindításnál nap)
3. Ha újraindítás történik a hét napját beállítása

> [!NOTE]
> A parancsfájl művelet csak Linux-alapú HDInsight-fürtökkel 2016 augusztusától 1. után létrehozott fog működni. Javítások hatékony lesz, csak akkor, ha a virtuális gép újraindítása van. 
>

## <a name="how-to-use-the-script"></a>A parancsfájl használata 

Ha a parancsfájl szükséges a következő információkat:
1. A parancsfájl helyét: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv01/os-patching-reboot-config.sh.  HDInsight ezt az URI használatával keresse meg és futtassa a parancsfájlt a fürt összes virtuális gépet.
  
2. A csomópont fürttípusok, amelyekkel a parancsfájl vonatkozik: headnode, workernode, zookeeper. Ezt a parancsfájlt a fürt minden csomópont típus kell alkalmazni. A csomóponttípus nem vonatkozik, ha a virtuális gépek az adott csomópont folytatódik a korábbi javítási ütemezés használata.


3.  A paraméter: Ez a parancsfájl három numerikus paramétert fogad:

    | Paraméter | Meghatározás |
    | --- | --- |
    | Engedélyezi/letiltja az automatikus újraindulását |0 vagy 1. A 0 érték letiltja az automatikus újraindulását 1 lehetővé teszi az automatikus újraindulását. |
    | gyakoriság |7-90 (a határokat is beleértve). A virtuális gépek, a számítógép újraindítása szükséges javítást újraindítás előtt várnia napok száma. |
    | Hét napja |1 és 7 (a határokat is beleértve). 1 érték azt jelzi, az újraindítás megtörténik egy hétfőn, a 7 pedig egy Sunday.For példában paraméterek használatával 1-60 2 eredmények automatikus újraindítás csak 60 naponta (legfeljebb) keddjén jelennek meg. |
    | Adatmegőrzés |A parancsfájlművelet egy meglévő fürthöz alkalmazásakor akkor is megjelölése a parancsfájl megőrzött. Új workernodes hozzáadódnak a méretezés műveletek során a fürt megőrzött parancsfájlok lépnek érvénybe. |

> [!NOTE]
> Ezt a parancsfájlt kell megjelölni, mert maradnak, ha a meglévő fürt. Ellenkező esetben a skálázási műveletek segítségével létrehozott új csomópontok fogja használni az alapértelmezett ütemezés javítását.
Ha a parancsfájl a Fürtlétrehozási folyamat részeként, állandó automatikusan.
>

## <a name="next-steps"></a>Következő lépések

A parancsfájl műveletével részletes lépéseit lásd: a következő részeket a [testreszabása Linuz-alapú HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster-linux.md):

* [Egy parancsfájlművelettel fürt létrehozása során](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [A parancsfájlművelet alkalmazása egy működő fürthöz](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
