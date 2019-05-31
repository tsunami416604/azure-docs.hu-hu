---
title: Az operációs rendszer javításai a Linux-alapú HDInsight-fürtök – Azure ütemezésének konfigurálása
description: Ismerje meg az operációs rendszer javításai a Linux-alapú HDInsight-fürtök ütemezés konfigurálása.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: cfbd68e66730fc338130bc16849fe0b2f4abd6be
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244411"
---
# <a name="os-patching-for-hdinsight"></a>Operációs rendszer javításai a HDInsight 

> [!IMPORTANT]
> Ubuntu-rendszerképek válnak elérhetővé az új HDInsight-fürt létrehozása közzétehető 3 hónapon belül. Január a 2019-tól futó fürtök vannak **nem** automatikus tudjon fókuszálni. Ügyfeleink a futó fürt javítására irányuló szkriptműveletek vagy más mechanizmusok kell használnia. Újonnan létrehozott fürtök mindig a legújabb elérhető frissítések, többek között a legújabb biztonsági javítások tartozik.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Az operációs rendszer javítási ütemezése a Linux-alapú HDInsight-fürtök konfigurálása
Egy HDInsight-fürtön lévő virtuális gépek kell alkalmanként indítani, hogy fontos biztonsági javításokat is telepíthető. 

A jelen cikkben ismertetett szkriptműveletek használatával, módosíthatja az operációs rendszer következő javítási ütemezése:
1. Telepítse a teljes operációs rendszer frissítése vagy csak a biztonsági frissítések
2. A virtuális gépet

> [!NOTE]  
> Ez a parancsprogram-művelet csak a 2016. augusztus 1. után létrehozott Linux-alapú HDInsight-fürtökkel működik. Javítások től lép érvénybe, csak akkor, ha a virtuális gépek indulnak újra. Ez a szkript automatikusan nem érvényes az összes jövőbeli frissítés ciklusok frissítéseit. Futtassa a szkriptet minden alkalommal új frissítéseket kell alkalmazni annak érdekében, hogy telepítse a frissítéseket, és a virtuális gépet.

## <a name="how-to-use-the-script"></a>A parancsfájl használata 

Ha ez a szkript használatához a következő információkat:
1. A parancsprogram helye: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh.  HDInsight ezt az URI használatával keresse meg és futtassa a parancsfájlt a fürt összes virtuális gépet.
  
2. A alkalmazni a parancsfájl fürtcsomópont-típusokat: átjárócsomópontjával, workernode, zookeeper. Ez a szkript a fürt minden csomópont típusokat kell alkalmazni. Ha ez a csomópont típusa nem alkalmazható, majd a virtuális gépek adott csomóponttípus nem frissül.


3.  A paraméter: Ez a szkript egy numerikus paramétert fogad el:

    | Paraméter | Meghatározás |
    | --- | --- |
    | Csak teljes operációs rendszer frissítéseinek/Install biztonsági frissítések telepítése |0 vagy 1. A 0 érték biztonsági frissítéseket telepíti, csak a 1 telepíti a teljes operációs rendszer frissítése közben. Ha a paraméter nincs megadva, az alapértelmezett érték a 0. |

> [!NOTE]  
> Ez a szkript, egy meglévő fürthöz alkalmazásakor megőrzött kell megjelölni. Ellenkező esetben a méretezési műveletek során létrehozott minden új csomópontok fogja használni az alapértelmezett javítási ütemezése.  Ha a parancsfájl a Fürtlétrehozási folyamat részeként, állandó automatikusan.


## <a name="next-steps"></a>További lépések

A script actionnel részletes lépéseit lásd a következő szakaszai a [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md):

* [Fürt létrehozása során egy parancsfájlművelettel](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Egy futó fürt szkriptműveletet vonatkozik](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
