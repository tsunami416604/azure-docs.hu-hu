---
title: Az Apache datafu és a HDInsight - Azure Apache Pig együttes használata
description: Apache Pig-DataFu gyűjteménye, kódtárak Apache hadoop Apache Pig való használatra. Ismerje meg, hogy Ön is a datafu és a Pig együttes a HDInsight-fürtön.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/16/2018
ms.author: hrasheed
ms.openlocfilehash: d67c3e452da05c626721d4c3144e612e6f9e0af4
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338444"
---
# <a name="use-apache-datafu-pig-with-apache-pig-on-hdinsight"></a>Az Apache DataFu a Pig használata a HDInsight az Apache Pig

Ismerje meg, hogyan lehet Apache DataFu a Pig használata a HDInsight.

Apache Pig-DataFu gyűjteménye, nyílt forráskódú függvénytárak Apache hadoop Apache Pig való használatra.
A DataFu Pig további információkért lásd: [ https://datafu.apache.org/ ](https://datafu.apache.org/).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés.

* Egy Azure HDInsight-fürt (Linux vagy Windows-alapú)

  > [!IMPORTANT]  
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Egy alapszintű ismeretét [a HDInsight Apache Pig használatával](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>A Linux-alapú HDInsight DataFu telepítése

> [!IMPORTANT]  
> DataFu 3.3 és magasabb szintű Linux-alapú fürtök verziója, valamint a Windows-alapú fürtök van telepítve. Nincs telepítve a Linux-alapú fürtök 3.3-as, mint korábban.
>
> Ha egy Windows-alapú fürt vagy egy magasabb, mint 3.3-as verziója Linux-alapú fürtöt használ, akkor kihagyhatja ezt a szakaszt.

DataFu letölthető, és telepítve van a Maven tárházból. Használja a következő lépések szükségesek, és adja hozzá a HDInsight-fürt verzió azonosításához:

> [!WARNING]  
> DataFu verzióiban előfordulhat, hogy követelményei, amely a HDInsight nem teljesülnek. Például ha DataFu egy régebbi verzióját használja, szükség lehet a Pig egy eltérő verziójú, mint a HDInsight tartalmát is.

### <a name="find-a-version"></a>A verzió megkereséséhez

1. Navigáljon a böngészőben https://mvnrepository.com/artifact/org.apache.datafu/datafu-pig , és keresse meg a szükséges verziót.

2. Válassza ki a csatolt verziószám.

3. Válassza ki __az összes megtekintése__ megtekintéséhez az összes fájlt.

4. A fájlok listáját keresse meg a .JAR kiterjesztésű fájlt. Általában ez a fájl lesz legnagyobb szerepel a listában, összes függőséget tartalmaz. Kattintson a jobb gombbal a hivatkozásra, és másolja a hivatkozás címe.

### <a name="download-datafu-to-hdinsight"></a>A HDInsight DataFu letöltése

1. A Linux-alapú HDInsight-fürthöz SSH használatával csatlakozhat. További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. A következő paranccsal töltse le a DataFu jar-fájlt a wget segédprogrammal:

    > [!IMPORTANT]  
    > A hivatkozás a parancsban cserélje le a korábban vágólapra másolt URL-CÍMÉT.

    ```
    wget https://central.maven.org/maven2/org/apache/datafu/datafu-pig/1.4.0/datafu-pig-1.4.0.jar
    ```

3. Ezután feltöltheti a fájlt a HDInsight-fürthöz tartozó alapértelmezett tároló. Helyezi el a fájlt az alapértelmezett tároló lehetővé teszi az összes csomóponton a fürtben.

    > [!IMPORTANT]  
    > Cserélje le a fájl nevét a verziószámot a letöltött verzió.

    ```
    hdfs dfs -put datafu-pig-1.4.0.jar /example/jars
    ```

    > [!NOTE]  
    > Az előző parancs tárolja a fájlt `/example/jars` mivel ez a könyvtár már létezik a fürttároló. Használhat bármilyen olyan helyre, a HDInsight-fürt tárhely kívánja.

## <a name="use-datafu-with-pig"></a>A datafu és a Pig együttes

A jelen szakaszban ismertetett lépések feltételezik, hogy Ön ismeri a Pig és HDInsight használatakor. A Pig használata a HDInsight további információkért lásd: [a Pig használata a HDInsight-](hdinsight-use-pig.md).

> [!IMPORTANT]  
> Ha manuálisan telepítette az előző szakaszban ismertetett lépések DataFu, regisztrálnia kell, mielőtt használja.
>
> * Ha a fürt használja az Azure Storage, használjon egy `wasb://` elérési útja. Például: `register wasb:///example/jars/datafu-pig-1.4.0.jar`.
>
> * Ha a fürt az Azure Data Lake Store Gen2 használ, egy `abfs://` elérési útja. Például: `register abfs://home/example/jars/datafu-pig-1.4.0.jar`.
>
> * Ha a fürt az Azure Data Lake Store Gen1 használ, egy `adl://` elérési útja. Például: `register adl://home/example/jars/datafu-pig-1.4.0.jar`.

Gyakran meghatározhat egy DataFu függvény aliasa. Az alábbi példa meghatározza az alias `SHA`:

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

Ezután használhatja ezt az aliast a Pig Latin parancsfájl egy kivonatot a bemeneti adatok előállításához. Például a következő kódot a kivonat értékével rendelkező váltja fel a helyet, a bemeneti adatok:

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

A következő kimenet állít elő:

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>További lépések

További információ a DataFu és a Pig a következő dokumentumokban talál:

* [Ismerkedés az Apache DataFu Pig](https://datafu.apache.org/docs/datafu/getting-started.html).
* [Az Apache Pig használata a HDInsight](hdinsight-use-pig.md)
