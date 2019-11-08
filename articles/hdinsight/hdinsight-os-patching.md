---
title: OPERÁCIÓSRENDSZER-javítási ütemterv konfigurálása az Azure HDInsight-fürtökhöz
description: Útmutató a Linux-alapú HDInsight-fürtök operációsrendszer-javítási ütemtervének konfigurálásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a97a03f7ef20ae56cec04341fe76b79ee657547b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748480"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>A Linux-alapú HDInsight-fürtök operációsrendszer-javítási ütemtervének konfigurálása

> [!IMPORTANT]
> Az Ubuntu-lemezképek az új Azure HDInsight-fürt létrehozása során elérhetővé válnak a közzétételtől számított három hónapon belül. Január 2019-én a futó fürtök nem automatikusan lettek kijavítani. Az ügyfeleknek parancsfájl-műveleteket vagy más mechanizmusokat kell használniuk a futó fürtök javításához. Az újonnan létrehozott fürtök mindig rendelkeznek a legújabb elérhető frissítésekkel, beleértve a legfrissebb biztonsági javításokat is.

A HDInsight támogatást nyújt a fürtben lévő általános feladatok végrehajtásához, például az operációsrendszer-javítások, a biztonsági frissítések és a csomópontok újraindításának telepítéséhez. Ezek a feladatok a következő két parancsfájl használatával végezhetők el, amelyek [parancsfájl-műveletként](hdinsight-hadoop-customize-cluster-linux.md)futtathatók, és paraméterekkel konfigurálhatók:

- `schedule-reboots.sh` – azonnali újraindítás vagy újraindítást ütemezhet a fürtcsomópontokon.
- `install-updates-schedule-reboots.sh` – telepítse az összes frissítést, csak a kernel + biztonsági frissítéseket, vagy csak a kernel frissítéseit.

> [!NOTE]  
> A parancsfájl-műveletek nem fogják automatikusan alkalmazni a frissítéseket az összes jövőbeli frissítési ciklusra. Futtassa a parancsfájlokat minden alkalommal, amikor új frissítéseket kell alkalmazni a frissítések telepítéséhez, majd indítsa újra a virtuális gépet.

## <a name="restart-nodes"></a>Csomópontok újraindítása
  
A parancsfájl [-újraindítások](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh), beállítja a fürtön lévő gépeken végrehajtandó újraindítás típusát. A parancsfájl-művelet elküldésekor állítsa be úgy, hogy mindhárom csomópont-típusra vonatkozzon: a fő csomópont, a feldolgozói csomópont és a Zookeeper. Ha a parancsfájl nincs alkalmazva a csomópont típusára, az adott csomóponthoz tartozó virtuális gépek nem lesznek frissítve vagy újraindulva.

A `schedule-reboots script` egy numerikus paramétert fogad el:

| Paraméter | Elfogadott értékek | Meghatározás |
| --- | --- | --- |
| Végrehajtandó újraindítás típusa | 1 vagy 2 | Az 1 érték lehetővé teszi az ütemezés újraindítását (12-24 órán belül ütemezve). A 2 érték azonnali újraindítást tesz lehetővé (5 perc). Ha nincs megadva paraméter, az alapértelmezett érték 1. |  

## <a name="install-updates-and-restart-nodes"></a>Frissítések telepítése és a csomópontok újraindítása

A szkript [Install-Updates-Schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) különböző típusú frissítések telepítésére és a virtuális gép újraindítására szolgáló lehetőségeket biztosít.

A `install-updates-schedule-reboots` parancsfájl két numerikus paramétert fogad el, az alábbi táblázatban leírtak szerint:

| Paraméter | Elfogadott értékek | Meghatározás |
| --- | --- | --- |
| Telepítendő frissítések típusa | 0, 1 vagy 2 | A 0 érték csak a kernel frissítéseit telepíti. Az 1 érték minden frissítést telepít, és a 2 csak a kernel + biztonsági frissítéseket telepíti. Ha nincs megadva paraméter, az alapértelmezett érték a 0. |
| Végrehajtandó újraindítás típusa | 0, 1 vagy 2 | A 0 érték letiltja az újraindítást. Az 1 érték lehetővé teszi az ütemezett újraindítást, a 2 pedig lehetővé teszi az azonnali újraindítást. Ha nincs megadva paraméter, az alapértelmezett érték a 0. A felhasználónak módosítania kell az 1. bemeneti paramétert a 2. paraméterre. |

> [!NOTE]
> Azt követően, hogy egy meglévő fürtre alkalmazza, meg kell jelölnie egy parancsfájlt. Ellenkező esetben a skálázási műveletekkel létrehozott új csomópontok az alapértelmezett javítási ütemtervet fogják használni. Ha a parancsfájlt a fürt létrehozási folyamatának részeként alkalmazza, a rendszer automatikusan megőrzi azt.

## <a name="next-steps"></a>További lépések

A parancsfájl-műveletek használatával kapcsolatos konkrét lépésekért tekintse meg a [Linux-alapú HDInsight-fürtök testre szabása parancsfájl-művelettel](hdinsight-hadoop-customize-cluster-linux.md)című szakaszt a következő részekben:

* [Parancsfájl-művelet használata a fürt létrehozása során](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Parancsfájl-művelet alkalmazása futó fürtre](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
