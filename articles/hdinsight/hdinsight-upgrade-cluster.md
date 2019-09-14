---
title: HDInsight-fürt frissítése újabb verzióra – Azure
description: Útmutató az Azure HDInsight-fürt újabb verzióra való frissítéséhez.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: a62da0d27d21589f7b75a89cc184ce9f456f1fed
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960700"
---
# <a name="upgrade-hdinsight-cluster-to-a-newer-version"></a>HDInsight-fürt frissítése újabb verzióra
A legújabb HDInsight funkciók kihasználásához javasoljuk, hogy a HDInsight-fürtök frissítése a legújabb verzióra történjen. A HDInsight-fürt verzióinak frissítéséhez kövesse az alábbi irányelveket.

> [!NOTE]  
> A HDInsight támogatott verzióival kapcsolatos információkért lásd: a [HDInsight-összetevők verziói](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="upgrade-tasks"></a>Frissítési feladatok
A HDInsight-fürt frissítésének munkafolyamata a következő.

![Munkafolyamat-diagram frissítése](./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png)

1. Olvassa el a jelen dokumentum egyes szakaszait, és Ismerje meg, hogy milyen módosítások szükségesek a HDInsight-fürt frissítésekor.
2. Hozzon létre egy fürtöt tesztelési/minőségi megbízhatósági környezetben. A fürtök létrehozásáról további információt a [Linux-alapú HDInsight-fürtök létrehozásának megismerését](hdinsight-hadoop-provision-linux-clusters.md) ismertető témakörben talál.
3. Meglévő feladatok, adatforrások és elsüllyedés másolása az új környezetbe.
4. Az érvényesítési teszt végrehajtásával győződjön meg arról, hogy a feladatok a várt módon működnek az új fürtön.

Miután meggyőződött róla, hogy minden a várt módon működik, ütemezze az áttelepítéshez szükséges állásidőt. A leállás során hajtsa végre a következő műveleteket:

1.  A fürt csomópontjain helyileg tárolt átmeneti adattárolás biztonsági mentése. Ha például közvetlenül egy Head csomóponton van tárolva adat.
2.  Törölje a meglévő fürtöt.
3.  Hozzon létre egy fürtöt ugyanabban a VNET-alhálózatban, amely a legújabb (vagy támogatott) HDI-verziót használja ugyanazzal az alapértelmezett adattárral, amelyet az előző fürt használ. Ez lehetővé teszi, hogy az új fürt folytassa a munkát a meglévő termelési adataival.
4.  Importálja a biztonsági mentés alatt lévő összes átmeneti adatkészletet.
5.  Feladatok indítása/a feldolgozás folytatása az új fürt használatával.

## <a name="next-steps"></a>További lépések
* [Ismerje meg, hogyan hozhat létre Linux-alapú HDInsight-fürtöket](hdinsight-hadoop-provision-linux-clusters.md)
* [Csatlakozás a HDInsighthoz SSH-val](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Linux-alapú fürt kezelése Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)

