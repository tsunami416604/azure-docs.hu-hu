---
title: Az operációs rendszer javítási ütemezésének konfigurálása az Azure HDInsight-fürtökhöz
description: Ismerje meg, hogyan konfigurálhatja az operációs rendszer javítási ütemezését linuxos HDInsight-fürtökhöz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206860"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux alapú HDInsight-fürtök operációsrendszer-javítási ütemezésének konfigurálása

> [!IMPORTANT]
> Ubuntu lemezképek elérhetővé válnak az új Azure HDInsight-fürt létrehozása a közzétételtől számított három hónapon belül. 2019 januárjától a futó fürtök nem lesznek automatikusan javítva. Az ügyfelekparancsfájl-műveleteket vagy más mechanizmusokat kell használniuk a futó fürt javításához. Az újonnan létrehozott fürtök mindig a legújabb elérhető frissítésekkel rendelkeznek, beleértve a legújabb biztonsági javításokat is.

A HDInsight támogatást nyújt a fürtön végzett gyakori feladatok végrehajtásához, például az operációs rendszer javításainak telepítéséhez, a biztonsági frissítésekhez és a csomópontok újraindításához. Ezeket a feladatokat a következő két parancsfájl használja, amelyek [parancsfájlműveletként](hdinsight-hadoop-customize-cluster-linux.md)futtathatók, és paraméterekkel konfigurálhatók:

- `schedule-reboots.sh`- Azonnal indítsa újra, vagy ütemezze az újraindítást a fürtcsomópontokon.
- `install-updates-schedule-reboots.sh`- Telepítse az összes frissítést, csak kernel + biztonsági frissítéseket, vagy csak kernel frissítéseket.

> [!NOTE]  
> A parancsfájlműveletek nem alkalmazzák automatikusan a frissítéseket az összes jövőbeli frissítési ciklusra. Futtassa a parancsfájlokat minden alkalommal, amikor új frissítéseket kell alkalmazni a frissítések telepítéséhez, majd indítsa újra a virtuális gép.

## <a name="preparation"></a>Előkészítés

Javítás egy reprezentatív, nem éles környezetben, mielőtt éles környezetben üzembe helyezne. Dolgozzon ki egy tervet, hogy megfelelően tesztelje a rendszert, mielőtt a tényleges javítás.

Időről időre, egy ssh munkamenet a fürtdel, előfordulhat, hogy egy üzenetet, hogy a frissítés érhető el. Az üzenet a következőképpen nézhet ki:

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

A javítás nem kötelező, és saját belátása szerint.

## <a name="restart-nodes"></a>Csomópontok újraindítása
  
A parancsfájl [ütemezése-újraindítások](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh), beállítja az újraindítás típusát, amely a fürt ben lévő gépeken fog végrehajtani. A parancsfájlművelet elküldésekor állítsa be, hogy mindhárom csomóponttípusra vonatkozzon: főcsomópontra, feldolgozócsomópontra és zookeeperre. Ha a parancsfájl nincs alkalmazva egy csomóponttípusra, az adott csomóponttípus virtuális gépei nem frissülnek vagy nem indulnak újra.

A `schedule-reboots script` számegy numerikus paramétert fogad el:

| Paraméter | Elfogadott értékek | Meghatározás |
| --- | --- | --- |
| A végrehajtandó újraindítás típusa | 1 vagy 2 | Az 1 érték lehetővé teszi az ütemezés újraindítását (12-24 óra múlva ütemezve). A 2 érték lehetővé teszi az azonnali újraindítást (5 perc alatt). Ha nincs megadva paraméter, az alapértelmezett érték 1. |  

## <a name="install-updates-and-restart-nodes"></a>Frissítések telepítése és csomópontok újraindítása

A parancsfájl [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) különböző típusú frissítések telepítésének és a virtuális gép újraindításának lehetőségeit biztosítja.

A `install-updates-schedule-reboots` parancsfájl két numerikus paramétert fogad el, az alábbi táblázatban leírtak szerint:

| Paraméter | Elfogadott értékek | Meghatározás |
| --- | --- | --- |
| A telepítandó frissítések típusa | 0, 1 vagy 2 | A 0 érték csak a kernel frissítéseket telepíti. Az 1 érték az összes frissítést telepíti, 2 pedig csak a kernel + biztonsági frissítéseket. Ha nincs megadva paraméter, az alapértelmezett érték 0. |
| A végrehajtandó újraindítás típusa | 0, 1 vagy 2 | A 0 érték letiltja az újraindítást. Az 1 érték lehetővé teszi az ütemezés újraindítását, a 2 pedig az azonnali újraindítást. Ha nincs megadva paraméter, az alapértelmezett érték 0. A felhasználónak módosítania kell az 1-es bemeneti paramétert a 2-es bemeneti paraméterre. |

> [!NOTE]
> A parancsfájlt egy meglévő fürtre való alkalmazása után meg kell jelölnie, hogy az megőrzött. Ellenkező esetben a méretezési műveletek kel létrehozott új csomópontok az alapértelmezett javítási ütemezést fogják használni. Ha a parancsfájlt a fürt létrehozási folyamatrészeként alkalmazza, az automatikusan megmarad.

## <a name="next-steps"></a>További lépések

A parancsfájlműveletek használatával kapcsolatos konkrét lépéseket a [Linux-alapú HDInsight-fürtök testreszabása parancsfájlművelettel](hdinsight-hadoop-customize-cluster-linux.md)című szakaszban olvassa el:

- [Parancsfájl-művelet használata a fürt létrehozása során](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Parancsfájlművelet alkalmazása futó fürtre](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
