---
title: OPERÁCIÓSRENDSZER-javítási ütemterv konfigurálása az Azure HDInsight-fürtökhöz
description: Útmutató a Linux-alapú HDInsight-fürtök operációsrendszer-javítási ütemtervének konfigurálásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: 102ae56bb9dce2898c14bdc710420759a527a9e9
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514697"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>A Linux-alapú HDInsight-fürtök operációsrendszer-javítási ütemtervének konfigurálása

> [!IMPORTANT]
> Az Ubuntu-lemezképek az új Azure HDInsight-fürt létrehozása során elérhetővé válnak a közzétételtől számított három hónapon belül. Január 2019-én a futó fürtök nem automatikusan lettek kijavítani. Az ügyfeleknek parancsfájl-műveleteket vagy más mechanizmusokat kell használniuk a futó fürtök javításához. Az újonnan létrehozott fürtök mindig rendelkeznek a legújabb elérhető frissítésekkel, beleértve a legfrissebb biztonsági javításokat is.

A HDInsight támogatást nyújt a fürtben lévő általános feladatok végrehajtásához, például az operációsrendszer-javítások, a biztonsági frissítések és a csomópontok újraindításának telepítéséhez. Ezek a feladatok a következő két parancsfájl használatával végezhetők el, amelyek [parancsfájl-műveletként](hdinsight-hadoop-customize-cluster-linux.md)futtathatók, és paraméterekkel konfigurálhatók:

- `schedule-reboots.sh` – azonnali újraindítás vagy újraindítást ütemezhet a fürtcsomópontokon.
- `install-updates-schedule-reboots.sh` – telepítse az összes frissítést, csak a kernel + biztonsági frissítéseket, vagy csak a kernel frissítéseit.

> [!NOTE]  
> A parancsfájl-műveletek nem fogják automatikusan alkalmazni a frissítéseket az összes jövőbeli frissítési ciklusra. Futtassa a parancsfájlokat minden alkalommal, amikor új frissítéseket kell alkalmazni a frissítések telepítéséhez, majd indítsa újra a virtuális gépet.

## <a name="preparation"></a>Előkészítés

Az éles környezetben történő üzembe helyezést megelőzően egy reprezentatív, nem éles környezethez tartozó javítás. Dolgozzon ki egy tervet a rendszer megfelelő teszteléséhez a tényleges javítás előtt.

A fürttel létesített SSH-munkamenetből időről időre üzenetet kaphat, hogy a frissítés elérhető. Az üzenet valahogy így néz ki:

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

A javítások opcionálisan és saját belátása szerint is megadhatók.

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

## <a name="next-steps"></a>Következő lépések

A parancsfájl-műveletek használatával kapcsolatos konkrét lépésekért tekintse meg a [Linux-alapú HDInsight-fürtök testre szabása parancsfájl-művelettel](hdinsight-hadoop-customize-cluster-linux.md)című szakaszt a következő részekben:

- [Parancsfájl-művelet használata a fürt létrehozása során](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
- [Parancsfájl-művelet alkalmazása futó fürtre](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
