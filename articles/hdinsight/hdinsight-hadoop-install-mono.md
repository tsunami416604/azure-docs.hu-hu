---
title: A HDInsight - Azure Mono telepítése vagy frissítése
description: Ismerje meg, hogy egy adott verzióját Mono használata a HDInsight-fürt. Mono segítségével .NET-alkalmazások futtatása a Linux-alapú HDInsight-fürtökön.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: jasonh
ms.custom: hdinsightactive
ms.openlocfilehash: 54e5a5b72627dc9cd2d842ccb24d10e2f9ab9dce
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957176"
---
# <a name="install-or-update-mono-on-hdinsight"></a>A HDInsight Mono telepítése vagy frissítése

Ismerje meg, hogyan telepítheti egy adott verzióját [Mono](https://www.mono-project.com) a HDInsight 3.4 vagy újabb verziója.

Mono a HDInsight 3.4 vagy újabb van telepítve, és a .NET-alkalmazások futtatására szolgál. A Mono minden HDInsight-verzióhoz mellékelt verzióját kapcsolatos tudnivalókat lásd: [HDInsight összetevők verziószámozása](hdinsight-component-versioning.md). A fürt egy másik verziót telepíti, használja a parancsfájlművelet ebben a dokumentumban. 

## <a name="how-it-works"></a>Működés

Ez a szkript a következő paramétert fogad el:

* __Monó verziószám__: A Mono telepítése verzióját. A verzió elérhetőnek kell lennie [ https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/ ](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/).

A parancsfájl telepíti a következő monó csomagokat:

* __mono teljes körű__

* __ca-certificates-mono__

## <a name="the-script"></a>A parancsfájl

__Parancsfájl helye__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__Követelmények__:

* A parancsfájl a alkalmazni kell a __átjárócsomópontokhoz__ és __munkavégző csomópontok__.

## <a name="to-use-the-script"></a>A parancsfájl használata

Ez a parancsfájl használata a HDInsight információkért lásd: a [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster) dokumentumot. A szkript az Azure Portalon, az Azure PowerShell vagy a klasszikus Azure CLI segítségével is használhatja.

Következő a parancsprogram-művelet dokumentumban, míg a következő URI-t használja:

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

A monó verzió van telepítve, akkor adja meg a verziószámot a __paraméterek__ mező. Adja meg például `5.4` Mono 5.4 telepítése.

> [!NOTE]
> Ez a szkript HDInsight konfigurálásakor jelölje meg a parancsprogramot __megőrzött__. Ez a beállítás lehetővé teszi, hogy a HDInsight a parancsfájl alkalmazandó műveletek a méretezés során hozzáadott feldolgozó csomópontokat.

## <a name="next-steps"></a>További lépések

Megtanulhatta, hogyan frissítése vagy a HDInsight egy adott verzióját Mono telepítése. További információ a HDInsight a Mono való használatához a .NET-alkalmazások a következő dokumentumokban talál:

* [A .NET használata a HDInsight MapReduce streameléshez](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [A Hive és a Piggel a HDInsight .NET használata](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [C#, HDInsight alatt futó Stormmal megoldásokat fejleszthet](storm/apache-storm-develop-csharp-visual-studio-topology.md)
* [.NET – megoldások áttelepítése Linux-alapú HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)

Szkriptműveletek használatával kapcsolatos további információkért lásd: [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md)
