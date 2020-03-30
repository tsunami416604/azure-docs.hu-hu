---
title: Apache Hive-könyvtárak a fürt létrehozása során – Azure HDInsight
description: Ismerje meg, hogyan adhat hozzá Apache Hive-könyvtárakat (jar-fájlokat) egy HDInsight-fürthöz a fürt létrehozása során.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: 0b746963cea5a950ba47d8b4dfeb074cb0910436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471023"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Egyéni Apache Hive-könyvtárak hozzáadása hdinsight-fürt létrehozásakor

Ismerje meg, hogyan töltheti be előre az [Apache Hive-könyvtárakat](https://hive.apache.org/) a HDInsighton. Ez a dokumentum a parancsfájlművelet használatával kapcsolatos információkat tartalmazza a függvénytárak fürtlétrehozása során történő előzetes betöltéséhez. A dokumentumlépéseivel hozzáadott könyvtárak globálisan elérhetők a Hive-ban – nincs szükség [az ADD JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) használatára a betöltésükhöz.

## <a name="how-it-works"></a>Működés

Fürt létrehozásakor parancsfájlművelettel módosíthatja a fürtcsomópontokat létrehozásukkor. A dokumentumban lévő parancsfájl egyetlen paramétert fogad el, amely a könyvtárak helye. Ezt a helyet egy Azure Storage-fiókban kell tárolni, és a könyvtárakat jar fájlokként kell tárolni.

A fürt létrehozása során a parancsfájl számba veszi a `/usr/lib/customhivelibs/` fájlokat, átmásolja őket a központi `hive.aux.jars.path` és a `core-site.xml` feldolgozócsomópontok könyvtárába, majd hozzáadja őket a fájl tulajdonságához. Linux-alapú fürtökön is frissíti `hive-env.sh` a fájlt a fájlok helyével.

A cikkben található parancsfájlművelet használatával a függvénytárak elérhetővé válikk **a WebHCat**és **a HiveServer2**hive ügyfélprogram használataesetén.

## <a name="the-script"></a>A szkript

**Szkript helye**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Követelmények

* A parancsfájlokat a **Head és** a Worker **csomópontokra**is alkalmazni kell.

* A telepíteni kívánt üvegeket **egyetlen tárolóban**kell tárolni az Azure Blob Storage-ban.

* A jar fájlok könyvtárát tartalmazó tárfiókot a létrehozás során csatolni **kell** a HDInsight-fürthöz. Vagy az alapértelmezett tárfióknak kell lennie, vagy a __Tárfiók beállításai__között hozzáadott fióknak kell lennie.

* A tároló WASB elérési útját paraméterként kell megadni a parancsfájlművelethez. Ha például az üvegeket egy **libs** nevű tárolóban tárolják egy **mystorage** `wasbs://libs@mystorage.blob.core.windows.net/`nevű tárfiókban, a paraméter a .

  > [!NOTE]  
  > Ez a dokumentum feltételezi, hogy már létrehozott egy tárfiókot, blob tároló, és feltöltötte a fájlokat.
  >
  > Ha még nem hozott létre tárfiókot, ezt az [Azure Portalon](https://portal.azure.com)keresztül teheti meg. Ezután egy segédprogram, például az [Azure Storage Explorer](https://storageexplorer.com/) segítségével hozzon létre egy tárolót a fiókban, és töltsön fel fájlokat.

## <a name="create-a-cluster-using-the-script"></a>Fürt létrehozása parancsfájl használatával

1. Indítsa el a fürt kiépítését a [HDInsight-fürtök linuxos kiépítése](hdinsight-hadoop-provision-linux-clusters.md)című lépések használatával, de ne fejezze be a kiépítést. Az Azure PowerShell vagy a HDInsight .NET SDK használatával is létrehozhat egy fürtöt ezzel a parancsfájllal. A módszerek használatáról a [HDInsight-fürtök testreszabása parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md)című témakörben talál további információt. Az Azure Portalon a **Configuration + pricing** lapon válassza a **+ Script művelet hozzáadása lehetőséget.**

1. A **Storage**esetében, ha a jar fájlok tárát tartalmazó tár fiók nem lesz más, mint a fürthöz használt fiók, töltse ki a **További tárfiókokat.**

1. **Parancsfájlműveletek**esetén adja meg a következő információkat:

    |Tulajdonság |Érték |
    |---|---|
    |Parancsfájl típusa|- Egyéni|
    |Név|Kódtárak |
    |Bash parancsfájl URI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Csomóponttípus(ok)|Fej, Munkás|
    |Paraméterek|Adja meg az üvegeket tartalmazó tároló- és tárfiók WASB-címét. Például: `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Az Apache Spark 2.1 esetén használja `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`ezt a bash szkript URI: .

1. Folytassa a fürt kiépítését a [Linux on HDInsight-fürtök kiépítése című részben leírtak szerint.](hdinsight-hadoop-provision-linux-clusters.md)

Miután a fürt létrehozása befejeződött, használhatja a kaptár on keresztül a `ADD JAR` Hive a hive-ból hozzáadott üvegek használata nélkül.

## <a name="next-steps"></a>További lépések

A Hive használatáról az [Apache Hive használata a HDInsight segítségével című](hadoop/hdinsight-use-hive.md) témakörben talál további információt.
