---
title: Telepítse vagy frissítse a HDInsight - Azure monó |} Microsoft Docs
description: Ismerje meg, hogy egy adott verziójához monó használata a HDInsight-fürthöz. Monó a Linux-alapú HDInsight-fürtökön futó .NET-alkalmazások futtatására szolgál.
services: hdinsight
documentationCenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.custom: hdinsightactive
ms.openlocfilehash: 165f1d8175c7c7b58a5eec02a208b81fe73cb5f9
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
ms.locfileid: "31400435"
---
# <a name="install-or-update-mono-on-hdinsight"></a>Telepítse vagy frissítse a HDInsight monó

Ismerje meg, hogyan telepíthet egy adott verziójához [monó](https://www.mono-project.com) HDInsight 3.4-es vagy újabb rendszerre.

Monó a HDInsight 3.4-es és újabb rendszer van telepítve, és .NET-alkalmazások futtatására szolgál. Monó részét képező egyes HDInsight-verzió verziójának információkért lásd: [HDInsight-összetevők verziószámozása](hdinsight-component-versioning.md). A fürt egy másik verzió telepítéséhez használja a parancsfájlművelet ebben a dokumentumban. 

## <a name="how-it-works"></a>Működés

Ez a parancsfájl fogadja el a következő paramétert:

* __Monó verziószáma__: monó telepítendő verzióját. A verzió elérhetőnek kell lennie [ https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/ ](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

A parancsfájl telepíti a következő monó csomagokat:

* __Monó befejezése__

* __ca-certificates-mono__

## <a name="the-script"></a>A parancsfájl

__Parancsfájl-hely__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Követelmények__:

* A parancsfájl azon kell alkalmazni a __átjárócsomópontokat__ és __munkavégző csomópontokhoz__.

## <a name="to-use-the-script"></a>A parancsfájl használata

Használja ezt a parancsfájlt a hdinsight eszközzel kapcsolatos további információkért lásd: a [testreszabása Linux-alapú HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentum. A parancsfájl az Azure-portálon, az Azure PowerShell vagy az Azure parancssori felület is használhatja.

Közben a következő a parancsfájl művelet dokumentumot, használja a következő URI Azonosítót:

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

Monó verzió van telepítve, akkor adja meg a verziószámot a __paraméterek__ mező. Adja meg például `5.4` monó 5.4 telepítéséhez.

> [!NOTE]
> Ezt a parancsfájlt a HDInsight konfigurálásakor jelölje meg a parancsprogramot __megőrzött__. Ez a beállítás lehetővé teszi, hogy a HDInsight a parancsfájl alkalmazandó műveletek a méretezés során hozzáadott munkavégző csomópontokhoz.

## <a name="next-steps"></a>További lépések

Rendelkezik megtudta, hogyan frissítse vagy a HDInsight monó meghatározott verzióját telepítse. A .NET-alkalmazások használata a HDInsight monó további információkért lásd a következő dokumentumokat:

* [Az adatfolyamként történő MapReduce a HDInsight .NET használata](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [A Hive és a Pig a HDInsight .NET használata](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [A HDInsight alatt futó Storm a C# megoldások fejlesztése](storm/apache-storm-develop-csharp-visual-studio-topology.md)
* [Linux-alapú HDInsight .NET megoldások áttelepítése](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Parancsfájlműveletek használatával kapcsolatos további információkért lásd: [testreszabása Linux-alapú HDInsight-fürtök parancsfájlművelet használatával](hdinsight-hadoop-customize-cluster-linux.md)