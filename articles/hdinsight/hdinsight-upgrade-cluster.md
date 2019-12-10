---
title: HDInsight-fürt frissítése újabb verzióra – Azure
description: Útmutató az Azure HDInsight-fürt újabb verzióra való frissítéséhez.
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 1a1d4a71786ebb1e68f59084086b3256a1c1ea40
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951156"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>HDInsight-fürt frissítése újabb verzióra

A legújabb HDInsight funkciók kihasználásához javasoljuk, hogy a HDInsight-fürtök frissítése a legújabb verzióra történjen. A HDInsight-fürt verzióinak frissítéséhez kövesse az alábbi irányelveket.

> [!NOTE]  
> A HDInsight támogatott verzióival kapcsolatos információkért lásd: a [HDInsight-összetevők verziói](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Frissítési feladatok

A HDInsight-fürt frissítésének munkafolyamata a következő.
![HDInsight-frissítési munkafolyamat-diagram](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Olvassa el a jelen dokumentum egyes szakaszait, és Ismerje meg, hogy milyen módosítások szükségesek a HDInsight-fürt frissítésekor.
2. Hozzon létre egy fürtöt tesztelési/minőségi megbízhatósági környezetben. A fürtök létrehozásáról további információt a [Linux-alapú HDInsight-fürtök létrehozásának megismerését](hdinsight-hadoop-provision-linux-clusters.md) ismertető témakörben talál.
3. Meglévő feladatok, adatforrások és elsüllyedés másolása az új környezetbe.
4. Az érvényesítési teszt végrehajtásával győződjön meg arról, hogy a feladatok a várt módon működnek az új fürtön.

Miután meggyőződött róla, hogy minden a várt módon működik, ütemezze az áttelepítéshez szükséges állásidőt. A leállás során hajtsa végre a következő műveleteket:

1. A fürt csomópontjain helyileg tárolt átmeneti adattárolás biztonsági mentése. Ha például közvetlenül egy Head csomóponton van tárolva adat.
1. [Törölje a meglévő fürtöt](./hdinsight-delete-cluster.md).
1. Hozzon létre egy fürtöt ugyanabban a VNET-alhálózatban, amely a legújabb (vagy támogatott) HDI-verziót használja ugyanazzal az alapértelmezett adattárral, amelyet az előző fürt használ. Ez lehetővé teszi, hogy az új fürt folytassa a munkát a meglévő termelési adataival.
1. Importálja a biztonsági mentés alatt lévő összes átmeneti adatkészletet.
1. Feladatok indítása/a feldolgozás folytatása az új fürt használatával.

## <a name="next-steps"></a>Következő lépések

* [Ismerje meg, hogyan hozhat létre Linux-alapú HDInsight-fürtöket](hdinsight-hadoop-provision-linux-clusters.md)
* [Csatlakozás a HDInsighthoz SSH-val](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Linux-alapú fürt kezelése Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)
* [HDInsight kibocsátási megjegyzései](./hdinsight-version-release.md)
