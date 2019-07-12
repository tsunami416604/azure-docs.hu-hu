---
title: Az operációs rendszer javítási ütemezése a Linux-alapú HDInsight-fürtök – Azure konfigurálása
description: Ismerje meg az operációs rendszer javításai a Linux-alapú HDInsight-fürtök ütemezés konfigurálása.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: efe74618b269000749f7ba6c24d35903e540dcfb
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657049"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Az operációs rendszer javítási ütemezése a Linux-alapú HDInsight-fürtök konfigurálása 

> [!IMPORTANT]
> Ubuntu-rendszerképek válnak elérhetővé az új Azure HDInsight-fürt létrehozása közzétehető három hónapban. Január a 2019-tól futó fürtök nem automatikus tudjon fókuszálni. Ügyfeleink a futó fürt javítására irányuló szkriptműveletek vagy más mechanizmusok kell használnia. Újonnan létrehozott fürtök mindig a legújabb elérhető frissítések, többek között a legújabb biztonsági javítások tartozik.

Néha előfordul újra kell indítani a virtuális gépek (VM) fontos biztonsági javítások telepítése HDInsight-fürtben.

A jelen cikkben ismertetett szkriptműveletek használatával módosíthatja az operációs rendszer következő javítási ütemezése:

1. Telepítse minden frissítést, vagy csak kernel + biztonsági frissítések vagy kernelfrissítés.
2. Hajtsa végre újra kell indítani az azonnali, vagy a virtuális gép az Újraindítás ütemezése.

> [!NOTE]  
> A jelen cikkben ismertetett szkriptműveletek csak a Linux-alapú HDInsight-fürtök 2016. augusztus 1. után létrehozott fog működni. Csak a virtuális gépek újraindítása után a javítások olyan hatékony.
> Szkriptműveletek, nem fog automatikusan alkalmazza a frissítéseket az összes jövőbeli frissítés ciklusokat. A parancsfájlok futtatása minden alkalommal, amikor új frissítéseket kell alkalmazni a frissítések telepítéséhez, és indítsa újra a virtuális gép.

## <a name="add-information-to-the-script"></a>Információ a szkript hozzáadása

Parancsfájl használata szükséges a következő információkat:

- A telepítés-frissítések – ütemezés-újraindítások parancsfájl helye: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight ezt az URI megkereséséhez, és futtassa a parancsfájlt a fürt összes virtuális gép használja. Ez a szkript telepítse a frissítéseket, és indítsa újra a virtuális gép lehetőségeket biztosít.
  
- Az ütemezés-újraindítások parancsfájl helye: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight ezt az URI megkereséséhez, és futtassa a parancsfájlt a fürt összes virtuális gép használja. Ez a szkript újraindítja a virtuális Gépet.
  
- A alkalmazni a parancsfájl fürtcsomópont-típusokat átjárócsomópontjával workernode és zookeeper. A szkript a fürt összes csomóponttípusok vonatkoznak. Ha a parancsfájl a csomópont típusa nem érvényes, a virtuális gépek adott csomóponttípus nem frissített vagy újraindítása.

- Az install-frissítések – ütemezés-újraindítások szkript két numerikus paramétert fogad el:

    | Paraméter | Meghatározás |
    | --- | --- |
    | Csak kernel-frissítések telepítése / telepítse az összes frissítés vagy telepítés kernel és biztonsági frissítések csak|0, 1 vagy 2. A 0 érték csak kernel frissítéseket telepíti. Az 1 érték telepíti a frissítéseket, és csak 2 telepíti a kernel és az összes biztonsági frissítések. Ha a paraméter nincs megadva, az alapértelmezett érték 0. |
    | Nincs újraindítás vagy engedélyezése ütemezés újraindítás vagy engedélyezése azonnali újraindítás |0, 1 vagy 2. A 0 érték letiltja az újraindítást. Az 1 érték lehetővé teszi, hogy az ütemezés újraindítása, és 2 lehetővé teszi, hogy azonnal újraindítja. Ha a paraméter nincs megadva, az alapértelmezett érték 0. A felhasználónak módosítania kell a bemeneti paraméter 1, 2. paraméter beviteli. |
   
 - Az ütemezés-újraindítások parancsfájl egy numerikus paramétert fogad el:

    | Paraméter | Meghatározás |
    | --- | --- |
    | Újraindítás vagy engedélyezése azonnali újraindítás ütemezés engedélyezése |1\. vagy 2. Az 1 érték lehetővé teszi, hogy az ütemezés újraindítás (ütemezett 12 – 24 óra). A 2 érték lehetővé teszi, hogy azonnal újraindítja (5 percen) belül. Ha a paraméter nincs megadva, a rendszer az alapértelmezett érték 1. |  

> [!NOTE]
> Egy szkript, megőrzött egy meglévő fürthöz való alkalmazása után kell megjelölni. Ellenkező esetben a méretezési műveletek során létrehozott minden új csomópontok fogja használni az alapértelmezett javítási ütemezése. Ha a parancsfájl a Fürtlétrehozási folyamat részeként alkalmazza, automatikusan azt rendelkezik megőrzi.


## <a name="next-steps"></a>További lépések

Parancsfájlműveletekkel, részletes lépéseit lásd a következő szakaszok a [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md):

* [Fürt létrehozása során egy parancsfájlművelettel](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Egy futó fürt szkriptműveletet vonatkozik](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
