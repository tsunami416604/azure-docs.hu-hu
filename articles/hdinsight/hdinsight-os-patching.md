---
title: Az operációs rendszer javításai a Linux-alapú HDInsight-fürtök – Azure ütemezésének konfigurálása
description: Ismerje meg az operációs rendszer javításai a Linux-alapú HDInsight-fürtök ütemezés konfigurálása.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a73866a8898042b546fa47d9c3d14ab4e58e9a12
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503238"
---
# <a name="os-patching-for-hdinsight"></a>Operációs rendszer javításai a HDInsight 

> [!IMPORTANT]
> Ubuntu-rendszerképek válnak elérhetővé az új HDInsight-fürt létrehozása közzétehető három hónapban. Január a 2019-tól futó fürtök vannak **nem** automatikus tudjon fókuszálni. Ügyfeleink a futó fürt javítására irányuló szkriptműveletek vagy más mechanizmusok kell használnia. Újonnan létrehozott fürtök mindig a legújabb elérhető frissítések, többek között a legújabb biztonsági javítások tartozik.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Az operációs rendszer javítási ütemezése a Linux-alapú HDInsight-fürtök konfigurálása
Egy HDInsight-fürtön lévő virtuális gépek kell alkalmanként indítani, hogy fontos biztonsági javításokat is telepíthető. 

Ebben a cikkben leírt parancsfájlműveletekkel, módosíthatja az operációs rendszer következő javítási ütemezése:
1. Frissítések telepítése vagy telepítés kernel + security csak, vagy csak a kernel frissítések telepítése.
2. Azonnali újraindítás vagy az ütemezés a virtuális gép újraindítását.

> [!NOTE]  
> Parancsfájl műveletek csak a 2016. augusztus 1. után létrehozott Linux-alapú HDInsight-fürtökkel működik. Javítások től lép érvénybe, csak akkor, ha a virtuális gépek indulnak újra. Ezek a szkriptek automatikusan nem érvényes az összes jövőbeli frissítés ciklusok frissítéseit. Minden alkalommal új frissítéseket annak érdekében, hogy telepítse a frissítéseket, és indítsa újra a virtuális gép alkalmazni kell a parancsfájlok futtatását.

## <a name="how-to-use-the-script"></a>A parancsfájl használata 

Ez a szkript használatához a következő információkat:
1. A telepítés-frissítések – ütemezés-újraindítások parancsfájl helye: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight ezt az URI használatával keresse meg és futtassa a parancsfájlt a fürt összes virtuális gépet. Ez a szkript telepítse a frissítéseket, és indítsa újra a virtuális gép lehetőségeket biztosít.
  
2. Az ütemezés-újraindítások parancsfájl helye: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight ezt az URI használatával keresse meg és futtassa a parancsfájlt a fürt összes virtuális gépet. Ez a szkript újraindítja a virtuális Gépet.
  
3. A alkalmazni a parancsfájl fürtcsomópont-típusokat: átjárócsomópontjával, workernode, zookeeper. Ez a szkript a fürt minden csomópont típusokat kell alkalmazni. Ez a csomópont típusa nem alkalmazható, ha majd a virtuális gépek adott csomóponttípus fog nem frissített vagy újraindítása.

4. A paraméter: Az install-frissítések – ütemezés-újraindítások szkript két numerikus paramétert fogad el:

    | Paraméter | Meghatározás |
    | --- | --- |
    | Csak kernel-frissítések telepítése / telepítse az összes frissítés vagy telepítés kernel és biztonsági frissítések csak |0 vagy 1 vagy 2. A 0 érték kernelfrissítés csak, telepíti a frissítéseket, és 2 telepíti kernel és az összes biztonsági frissítések csak 1 telepítése közben. Ha a paraméter nincs megadva, az alapértelmezett érték 0. |
    | Nincs újraindítás vagy engedélyezése ütemezés újraindítás vagy engedélyezése azonnali újraindítás |0 vagy 1 vagy 2. A 0 érték letiltja az újraindítás, 1: engedélyezi az ütemezés újraindítás és a 2 lehetővé teszi az azonnali újraindítását. Ha a paraméter nincs megadva, az alapértelmezett érték 0. Felhasználói bevitel kell 1, 2. paraméter bemeneti paraméter. |
   
 5. A paraméter: Az ütemezés-újraindítások parancsfájl egy numerikus paramétert fogad el:

    | Paraméter | Meghatározás |
    | --- | --- |
    | Újraindítás vagy engedélyezése azonnali újraindítás ütemezés engedélyezése |1\. vagy 2. Az 1 érték lehetővé teszi, hogy az ütemezés újraindítás (újraindítás ütemezett a következő 12 – 24 óra) során 2 lehetővé teszi, hogy azonnali újraindítás (5 percen) belül. Ha a paraméter nincs megadva, az alapértelmezett érték 1. |  

> [!NOTE] 
> A parancsfájl, a meglévő fürthöz alkalmazásakor megőrzött kell megjelölni. Ellenkező esetben a méretezési műveletek során létrehozott minden új csomópontok fogja használni az alapértelmezett javítási ütemezése.  Ha a parancsfájl a Fürtlétrehozási folyamat részeként, állandó automatikusan.


## <a name="next-steps"></a>További lépések

A script actionnel részletes lépéseit lásd a következő szakaszok a [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md):

* [Fürt létrehozása során egy parancsfájlművelettel](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Egy futó fürt szkriptműveletet vonatkozik](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
