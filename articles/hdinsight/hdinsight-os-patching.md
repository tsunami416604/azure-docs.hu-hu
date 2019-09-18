---
title: A Linux-alapú HDInsight-fürtök operációsrendszer-javítási ütemtervének konfigurálása – Azure
description: Útmutató a Linux-alapú HDInsight-fürtök operációsrendszer-javítási ütemtervének konfigurálásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 06111ec35a127cf17fdcc77ff717de7a4bc7299f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076861"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>A Linux-alapú HDInsight-fürtök operációsrendszer-javítási ütemtervének konfigurálása 

> [!IMPORTANT]
> Az Ubuntu-lemezképek az új Azure HDInsight-fürt létrehozása során elérhetővé válnak a közzétételtől számított három hónapon belül. Január 2019-én a futó fürtök nem automatikusan lettek kijavítani. Az ügyfeleknek parancsfájl-műveleteket vagy más mechanizmusokat kell használniuk a futó fürtök javításához. Az újonnan létrehozott fürtök mindig rendelkeznek a legújabb elérhető frissítésekkel, beleértve a legfrissebb biztonsági javításokat is.

Időnként a fontos biztonsági javítások telepítéséhez újra kell indítania a virtuális gépeket (VM) egy HDInsight-fürtben.

A cikkben ismertetett parancsfájl-műveletek használatával a következőképpen módosíthatja az operációsrendszer-javítási ütemtervet:

1. Telepítse az összes frissítést, vagy telepítsen csak a kernel + biztonsági frissítéseket vagy a kernel-frissítéseket.
2. Végezzen azonnali újraindítást, vagy ütemezzen egy újraindítást a virtuális gépen.

> [!NOTE]  
> Az ebben a cikkben ismertetett parancsfájl-műveletek csak a 2016 augusztus 1. után létrehozott Linux-alapú HDInsight-fürtökkel fognak működni. A javítások csak a virtuális gépek újraindítása után lépnek érvénybe.
> A parancsfájl-műveletek nem fogják automatikusan alkalmazni a frissítéseket az összes jövőbeli frissítési ciklusra. Futtassa a parancsfájlokat minden alkalommal, amikor új frissítéseket kell alkalmazni a frissítések telepítéséhez, majd indítsa újra a virtuális gépet.

## <a name="add-information-to-the-script"></a>Információk hozzáadása a parancsfájlhoz

A szkriptek használatához a következő információk szükségesek:

- A Install-Updates-Schedule-reboots parancsfájl helye https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh:.
    
   A HDInsight ezzel az URI-val keresi és futtatja a parancsfájlt a fürtön lévő összes virtuális gépen. Ez a parancsfájl a frissítések telepítésére és a virtuális gép újraindítására vonatkozó beállításokat tartalmaz.
  
- Az ütemterv-újraindítási parancsfájl helye: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   A HDInsight ezzel az URI-val keresi és futtatja a parancsfájlt a fürtön lévő összes virtuális gépen. Ez a szkript újraindítja a virtuális gépet.
  
- A fürtcsomópont azon típusai, amelyeken a parancsfájlt alkalmazza a átjárócsomóponthoz, a workernode és a Zookeeper. Alkalmazza a parancsfájlt a fürt összes csomópont-típusára. Ha a parancsfájl nincs alkalmazva a csomópont típusára, az adott csomóponthoz tartozó virtuális gépek nem lesznek frissítve vagy újraindulva.

- Az Install-Updates-Schedule-reboots parancsfájl két numerikus paramétert fogad el:

    | Paraméter | Meghatározás |
    | --- | --- |
    | Csak a kernel frissítéseinek telepítése/az összes frissítés telepítése/kernel + biztonsági frissítések telepítése|0, 1 vagy 2. A 0 érték csak a kernel frissítéseit telepíti. Az 1 érték minden frissítést telepít, és a 2 csak a kernel + biztonsági frissítéseket telepíti. Ha nincs megadva paraméter, az alapértelmezett érték a 0. |
    | Nincs újraindítás/az ütemezett újraindítás engedélyezése/azonnali újraindítás engedélyezése |0, 1 vagy 2. A 0 érték letiltja az újraindítást. Az 1 érték lehetővé teszi az ütemezett újraindítást, a 2 pedig lehetővé teszi az azonnali újraindítást. Ha nincs megadva paraméter, az alapértelmezett érték a 0. A felhasználónak módosítania kell az 1. bemeneti paramétert a 2. paraméterre. |
   
 - Az ütemezett újraindítások parancsfájl egy numerikus paramétert fogad el:

    | Paraméter | Meghatározás |
    | --- | --- |
    | Az ütemezett újraindítás engedélyezése/azonnali újraindítás engedélyezése |1 vagy 2. Az 1 érték lehetővé teszi az ütemezés újraindítását (12-24 órán belül ütemezve). A 2 érték azonnali újraindítást tesz lehetővé (5 perc). Ha nincs megadva paraméter, az alapértelmezett érték 1. |  

> [!NOTE]
> Azt követően, hogy egy meglévő fürtre alkalmazza, meg kell jelölnie egy parancsfájlt. Ellenkező esetben a skálázási műveletekkel létrehozott új csomópontok az alapértelmezett javítási ütemtervet fogják használni. Ha a parancsfájlt a fürt létrehozási folyamatának részeként alkalmazza, a rendszer automatikusan megőrzi azt.


## <a name="next-steps"></a>További lépések

A parancsfájl-műveletek használatával kapcsolatos konkrét lépésekért tekintse meg a [Linux-alapú HDInsight-fürtök testre szabása parancsfájl-művelettel](hdinsight-hadoop-customize-cluster-linux.md)című szakaszt a következő részekben:

* [Parancsfájl-művelet használata a fürt létrehozása során](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Parancsfájl-művelet alkalmazása futó fürtre](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
