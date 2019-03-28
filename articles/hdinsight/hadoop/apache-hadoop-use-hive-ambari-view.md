---
title: Az Apache Ambari-nézetek használható a Hive a HDInsight (Hadoop Apache) – Azure
description: Ismerje meg, hogyan használható a Hive-nézet a webböngészőből Hive-lekérdezések elküldéséhez. A Hive-nézet az Ambari webes felhasználói Felületet kapott a Linux-alapú HDInsight-fürt része.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: 978b865f6dd7e3427a0139e7e71ed4b2d937fbe5
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517300"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Az Apache Ambari Hive nézete használata a HDInsight Apache Hadoop-keretrendszerrel

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ismerje meg, hogyan futtathat Hive-lekérdezések Apache Ambari Hive-nézet használatával. A Hive-nézet létrehozásához, a optimalizálása és a Hive-lekérdezések futtatása a webböngészőből teszi lehetővé.

## <a name="prerequisites"></a>Előfeltételek

* A HDInsight Hadoop-fürt. Lásd: [HDInsight Linux első lépések](./apache-hadoop-linux-tutorial-get-started.md).
* Egy webes böngésző

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki a fürtöt.  Lásd: [fürtök listázása és megjelenítése](../hdinsight-administer-use-portal-linux.md#showClusters) útmutatást. A fürt megnyílik egy új portálpanelen.

2. A **fürt irányítópultjai**válassza **Ambari-nézetek**. Amikor a rendszer kéri, hitelesítéshez, használja a fürtre való bejelentkezéshez (alapértelmezett `admin`) fiók nevét és a fürt létrehozásakor megadott jelszót.

3. Nézetek listájáról válassza ki a __Hive-nézet__.

    ![A kiválasztott Hive-nézet](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    A Hive-nézet lap az alábbi képen láthatóhoz hasonló:

    ![A Hive-nézet a lekérdezés munkalap képe](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. Az a __lekérdezés__ lapon, a következő hiveql illessze be a munkalapra:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    Ezek az utasítások hajtsa végre a következő műveleteket:

   * `DROP TABLE`: Törli a tábla- és adatfájlt, abban az esetben, ha a tábla már létezik.

   * `CREATE EXTERNAL TABLE`: A Hive egy új "external" táblát hoz létre.
     Külső táblák csak a tábla definíciójának Hive tárolja. Az adatok az eredeti helyén marad.

   * `ROW FORMAT`: Bemutatja, hogyan van formázva az adatok. Ebben az esetben minden napló mezőinek vesszővel elválasztva.

   * `STORED AS TEXTFILE LOCATION`: Az adatok tárolására és szövegként tárolt jeleníti meg.

   * `SELECT`: Választja ki, ahol az oszlop t4 [hiba] értéket tartalmazza az összes sor számát.

   > [!IMPORTANT]  
   > Hagyja a __adatbázis__ a kijelölés __alapértelmezett__. Ebben a dokumentumban szereplő példák a HDInsight részét képező alapértelmezett adatbázist használja.

5. A lekérdezés indításához válassza **Execute** a munkalap alatt. A gomb narancssárga lesz, és a szöveg a következőre változik **leállítása**.

6. A lekérdezés futtatása után a **eredmények** lap megjeleníti a művelet eredményei. A következő szöveget a lekérdezés eredménye:

        loglevel       count
        [ERROR]        3

    Használhatja a **LOG** fülre kattintva megtekintheti a feladat által létrehozott adatok naplózása.

   > [!TIP]  
   > Töltse le, vagy a-eredményeket menteni a **műveletek** legördülő párbeszédpanel mellett a **eredmények** fülre.

### <a name="visual-explain"></a>Vizualizáció ismertetik.

A lekérdezésterv a Vizualizációk megjelenítéséhez válassza a **Visual ismertetik** fülre a munkalap alatt.

A **Visual ismertetik** nézet a lekérdezés akkor hasznosak, a folyamat az összetett lekérdezések ismertetése.

### <a name="tez-ui"></a>Tez UI

A lekérdezés a Tez felhasználói felület megjelenítéséhez válassza a **Tez felhasználói felület** fülre a munkalap alatt.

> [!IMPORTANT]  
> Tez nem használatos az összes lekérdezés megoldásához. Több lekérdezés használata a Tez nélkül oldható meg. 

## <a name="view-job-history"></a>Feladatelőzmények megtekintése

A __feladatok__ lapon Hive-lekérdezések előzményeit jeleníti meg.

![A feladatelőzmények képe](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>Adatbázistáblák

Használhatja a __táblák__ fülre, és a egy Hive-adatbázisban lévő táblák használata.

![A táblák lap képe](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>Mentett lekérdezések

Az a **lekérdezés** lapon szükség esetén mentheti lekérdezéseket. Miután egy lekérdezés mentéséhez felhasználhatja azt a __mentett lekérdezések__ fülre.

![Mentett lekérdezések lap képe](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]  
> Mentett lekérdezések az alapértelmezett fürttárolóhoz vannak tárolva. A mentett lekérdezések az elérési út alatt található `/user/<username>/hive/scripts`. Ezek egyszerű szövegként tárolt `.hql` fájlokat.
>
> Ha törli a fürtöt, de tartsa a tárolót, például egy segédprogram használható [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) vagy a Data Lake Storage Explorer (a a [az Azure Portal](https://portal.azure.com)) beolvasni a lekérdezéseket.

## <a name="user-defined-functions"></a>Felhasználó által definiált függvények

Hive kiterjesztheti a felhasználó által definiált függvények (UDF) keresztül. Egy UDF használatával HiveQL funkciók vagy logika, amely nem könnyen modellezett megvalósításához.

Deklarálja, és mentse az UDF-EK készletét használatával a **UDF** a Hive-nézet a felső fülön. Ezek a függvényekkel használható a **Lekérdezésszerkesztő**.

![Az UDF lap képe](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Miután hozzáadott egy UDF a Hive-nézet az egy **UDF-EK beszúrása** gomb alsó részén jelenik meg a **Lekérdezésszerkesztő**. Válassza ezt a bejegyzést az UDF-EK a Hive-nézetben definiált legördülő listáját jeleníti meg. Az UDF engedélyezze a lekérdezés egy UDF kiválasztásával HiveQL utasítások hozzá.

Például ha meghatározta a UDF-ben a következő tulajdonságokkal:

* Erőforrás neve: myudfs

* Erőforrás elérési útja: /myudfs.jar

* Az UDF-name: myawesomeudf

* Az UDF osztálynév: com.myudfs.Awesome

Használatával a **UDF-EK beszúrása** gomb megjeleníti ezt a nevet **myudfs**, a másik legördülő listából válassza ki az adott erőforráshoz definiált minden egyes UDF. Ebben az esetben **myawesomeudf**. Válassza ezt a bejegyzést ad hozzá a következő lekérdezés kezdete:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Ezután használhatja az UDF-ben a lekérdezésben. Például: `SELECT myawesomeudf(name) FROM people;`.

Az UDF-EK használata a HDInsight Hive-val további információkért tekintse meg a következő cikkeket:

* [Az Apache Hive és a HDInsight Apache Pig Python használatával](python-udf-hdinsight.md)
* [A HDInsight egy egyéni Apache Hive-UDF hozzáadása](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Hive-beállítások

Módosíthatja a különböző Hive-beállítások, például a Hive Tez (alapértelmezett), a MapReduce, a-végrehajtó motor módosítása.

## <a id="nextsteps"></a>Következő lépések

Általános információk a HDInsight Hive:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)

Információk az egyéb módon használhatja a Hadoop on HDInsight:

* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)
* [A HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md)
