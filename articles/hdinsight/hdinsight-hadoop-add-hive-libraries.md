---
title: Könyvtárak Apache Hive a fürt létrehozása során – Azure HDInsight
description: Megtudhatja, hogyan adhat hozzá Apache Hive kódtárakat (jar-fájlokat) egy HDInsight-fürthöz a fürt létrehozása során.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 51a93aaec4abdb2dd9d8fad042c079a48d4ea7a3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494834"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Egyéni Apache Hive-kódtárak hozzáadása a HDInsight-fürt létrehozásakor

Megtudhatja, hogyan tölthetők be [Apache Hive](https://hive.apache.org/) -kódtárak a HDInsight. Ez a dokumentum a fürt létrehozása során a kódtárak előzetes betöltésére szolgáló parancsfájl-művelettel kapcsolatos információkat tartalmaz. A jelen dokumentumban ismertetett lépések használatával hozzáadott könyvtárak globálisan elérhetők a kaptárban – nem szükséges a [jar hozzáadása](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) a betöltéshez.

## <a name="how-it-works"></a>Működési elv

Fürt létrehozásakor parancsfájl-művelettel módosíthatja a fürtcsomópontok létrehozását. A dokumentumban szereplő parancsfájl egyetlen paramétert fogad el, amely a kódtárak helye. Ennek a helynek egy Azure Storage-fiókban kell lennie, és a kódtárakat jar-fájlként kell tárolni.

A fürt létrehozása során a parancsfájl enumerálja a fájlokat, átmásolja azokat a fej és a munkavégző csomópontok `/usr/lib/customhivelibs/` könyvtárába, majd hozzáadja azokat a `core-site.xml` fájlban található `hive.aux.jars.path` tulajdonsághoz. Linux-alapú fürtökön a `hive-env.sh` fájlt is frissíti a fájlok helyével.

> [!NOTE]  
> A cikkben szereplő parancsfájl-műveletek használatával a kódtárak a következő helyzetekben érhetők el:
>
> * **Linux-alapú HDInsight** , ha kaptár-ügyfelet, **webhcaten**és **HiveServer2**használ.
> * **Windows-alapú HDInsight** – a struktúra-ügyfél és a **webhcaten**használata esetén.

## <a name="the-script"></a>A parancsfájl

**Parancsfájl helye**

**Linux-alapú fürtök**esetén: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

**Windows-alapú fürtök**esetén: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**Követelmények**

* A parancsfájlokat a **főcsomópontokra** és a **munkavégző csomópontokra**egyaránt alkalmazni kell.

* A telepíteni kívánt tégelyeket **egyetlen tárolóban**kell tárolni az Azure Blob Storageban.

* A jar **-fájlok** könyvtárát tartalmazó Storage-fióknak a létrehozás során a HDInsight-fürthöz kell kapcsolódnia. Az alapértelmezett Storage-fióknak vagy az __opcionális konfiguráción__keresztül hozzáadott fióknak kell lennie.

* A tároló WASB elérési útját paraméterként kell megadni a parancsfájl művelethez. Ha például az adattárolók a **mystorage**nevű Storage-fiókban a **libs** nevű tárolóban tárolódnak, akkor a paraméter a **wasb://libs\@mystorage.blob.Core.Windows.net/** lesz.

  > [!NOTE]  
  > Ez a dokumentum azt feltételezi, hogy már létrehozott egy Storage-fiókot, egy BLOB-tárolót, és feltöltötte a fájlokat.
  >
  > Ha nem hozott létre Storage-fiókot, azt a [Azure Portalon](https://portal.azure.com)keresztül teheti meg. Ezután használhat olyan segédprogramot, mint például a [Azure Storage Explorer](https://storageexplorer.com/) , amely létrehoz egy tárolót a fiókban, és feltölti a fájlokat.

## <a name="create-a-cluster-using-the-script"></a>Fürt létrehozása a parancsfájl használatával

> [!NOTE]  
> Az alábbi lépéseket követve hozzon létre egy Linux-alapú HDInsight-fürtöt. Windows-alapú fürt létrehozásához válassza a **Windows** lehetőséget fürtként a fürt létrehozásakor, és a bash-szkript helyett használja a Windows (PowerShell) parancsfájlt.
>
> A parancsfájl használatával a Azure PowerShell vagy a HDInsight .NET SDK használatával is létrehozhat fürtöt. A módszerek használatával kapcsolatos további információkért lásd: [HDInsight-fürtök testreszabása parancsfájl-műveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

1. A fürt kiépítésének megkezdéséhez használja a [HDInsight-fürtök Linuxon](hdinsight-hadoop-provision-linux-clusters.md)való üzembe helyezésének lépéseit, de ne fejezze be a telepítést.

2. A **választható konfiguráció** szakaszban válassza a **parancsfájlok műveletei**lehetőséget, és adja meg a következő információkat:

   * **Név**: adjon meg egy rövid nevet a parancsfájl-művelethez.

   * **Parancsfájl URI-ja**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh.

   * **Head**: ezt a beállítást kell bejelölni.

   * **Worker**: ezt a beállítást kell megnéznie.

   * **ZOOKEEPER**: hagyja üresen ezt a mezőt.

   * **Paraméterek**: adja meg az WASB-címeket az edényt tartalmazó tároló-és Storage-fiókhoz. Például **wasb://libs\@mystorage.blob.Core.Windows.net/** .

3. A **parancsfájl műveleteinek**alján a **kiválasztás** gombbal mentheti a konfigurációt.

4. A **választható konfiguráció** szakaszban válassza a **társított Storage-fiókok** lehetőséget, majd válassza a Storage- **Kulcs hozzáadása** hivatkozást. Válassza ki az edényt tartalmazó Storage-fiókot. Ezután a **Select** gombokkal mentheti a beállításokat, és visszaadja a **választható konfigurációt**.

5. A választható konfiguráció mentéséhez használja a Select ( **kiválasztás** ) gombot a **választható konfiguráció** szakasz alján.

6. Folytassa a fürt kiépítését a [Linuxon futó HDInsight-fürtök kiépítése](hdinsight-hadoop-provision-linux-clusters.md)című témakörben leírtak szerint.

A fürt létrehozása után az ezen a szkripten keresztül hozzáadott tégelyeket használhatja a kaptárból anélkül, hogy a `ADD JAR` utasítást kellene használnia.

## <a name="next-steps"></a>További lépések

További információ a kaptár [használatáról: Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
