---
title: 'Rövid útmutató: Hozzon létre egy Apache Spark-készletet (előzetes verzió) az Azure Synapse Analytics szolgáltatásban'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre a webes eszközökkel egy Apache Spark-készlet (előzetes verzió) az Azure Synapse Analytics szolgáltatásban, és hogyan futtathat egy Spark SQL-lekérdezést.
services: synapse-analytics
author: euangMS
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: quickstart
ms.date: 04/15/2020
ms.openlocfilehash: 5762c074b825c1282959c509c2c72d232f0a238c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424299"
---
# <a name="quickstart-create-an-apache-spark-pool-preview-in-synapse-analytics-using-web-tools"></a>Rövid útmutató: Apache Spark-készlet (előzetes verzió) létrehozása a Synapse Analytics szolgáltatásban webes eszközökkel

Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre Apache Spark-készletet (előzetes verziót) az Azure Synapse Analytics szolgáltatásban webes eszközök használatával. Ezután megtanulhatja, hogy csatlakozzon az Apache Spark-készlethez, és futtasson Spark SQL-lekérdezéseket a fájlokon és a táblákon. Az Apache Spark a memóriában végzett feldolgozás segítségével teszi lehetővé a gyors adatelemzést és fürtszámítást. A Spark synapse Analytics szolgáltatással kapcsolatos további [tudnivalókat az Áttekintés: Apache Spark az Azure Synapse Analytics webhelyen című témakörben találja.](apache-spark-overview.md)

> [!IMPORTANT]
> A Spark-példányok számlázása percenként van értékelve, függetlenül attól, hogy használja-e őket, vagy sem. Győződjön meg róla, hogy leállítja a Spark-példányt, miután befejezte a használatát, vagy állítson be egy rövid időtúltöltést. További információkért lásd a cikk **Az erőforrások eltávolítása** című szakaszát.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="create-an-apache-spark-pool"></a>Apache Spark-készlet létrehozása

Ez a cikk bemutatja, hogyan hozhat létre egy új Apache Spark-készlet webes eszközök használatával.

## <a name="create-a-notebook"></a>Jegyzetfüzet létrehozása

A notebook olyan interaktív környezet, amely támogatja a különböző programozási nyelveket. A notebook lehetővé teszi, hogy kölcsönhatásba lépnek az adatokkal, kombinálják a kódot a markdownnal, szöveget és egyszerű vizualizációkat hajtsanak végre.

1. A használni kívánt Synapse Analytics-munkaterület Azure Portal nézetében válassza a **Synapse Studio indítása**lehetőséget.
2. Miután elindította a Synapse Analytics Studio alkalmazást, válassza **a Fejlesztés**lehetőséget. Ezután vigye az egérmutatót a **Jegyzetfüzetek** bejegyzés fölé. Válassza ki a három pontot (**...**).
3. Itt válassza az **Új jegyzetfüzet lehetőséget.** Egy új jegyzetfüzet jön létre, és automatikusan létrehozott névvel nyílik meg.
  ![Új jegyzetfüzet](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-new-notebook.png "Új jegyzetfüzet")

4. A **Tulajdonságok** ablakban adja meg a jegyzetfüzet nevét.
5. Kattintson az eszköztár **Közzététel**gombjára.
6. Ha csak egy Apache Spark-készlet van a munkaterületen, akkor alapértelmezés szerint be van jelölve. A legördülő menü segítségével válassza ki a megfelelő Apache Spark-készletet, ha nincs kiválasztva.
7. Kattintson **a Kód hozzáadása gombra.** Az alapértelmezett `Pyspark`nyelv a . Pyspark és Spark SQL vegyesen fog használni, így az alapértelmezett választás rendben van.
8. Ezután hozzon létre egy egyszerű Spark DataFrame objektumot a kezeléshez. Ebben az esetben kódból hozza létre. Három sor és három oszlop van:

   ```python
   new_rows = [('CA',22, 45000),("WA",35,65000) ,("WA",50,85000)]
   demo_df = spark.createDataFrame(new_rows, ['state', 'age', 'salary'])
   demo_df.show()
   ```

9. Most futtassa a cellát az alábbi módszerek egyikével:

   - Nyomja **le a SHIFT + ENTER billentyűkombinációt.**
   - Válassza a cella bal oldalán található kék lejátszás ikont.
   - Válassza az **Eszköztár Összes futtatása** gombját.

   ![Adatkeret-objektum létrehozása](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-create-data-frame-object.png "Kimenet a Spark-feladatból")

10. Ha az Apache Spark készletpéldány még nem fut, automatikusan elindul. Láthatja az Apache Spark készletpéldány állapotát a futtatott cella alatt, valamint a notebook alján található állapotpanelen. Attól függően, hogy a méret a medence, kezdve kell 2-5 perc. Miután a kód futása befejeződött, a cella alatti információk azt mutatják, hogy mennyi ideig tartott a futtatás és a végrehajtás. A kimeneti cellában a kimenet látható.

    ![Cella végrehajtásának kimenete](./media/apache-spark-notebook-create-spark-use-sql/run-cell-with-output.png "Kimenet a Spark-feladatból")

11. Az adatok most már létezik egy DataFrame onnan használhatja az adatokat számos különböző módon. A rövid útmutató többi részéhez különböző formátumokban lesz rá szükség.
12. Írja be az alábbi kódot egy másik cellába, és futtassa azt, ez létrehoz egy Spark táblát, egy CSV-t és egy Parketta fájlt, amelyek mindegyike tartalmazza az adatok másolatát:

    ```python
     demo_df.createOrReplaceTempView('demo_df')
     demo_df.write.csv('demo_df', mode='overwrite')
     demo_df.write.parquet('abfss://<<TheNameOfAStorageAccountFileSystem>>@<<TheNameOfAStorageAccount>>.dfs.core.windows.net/demodata/demo_df', mode='overwrite')
    ```

    Ha a tárolókezelőt használja, a fent használt fájl írásának két különböző módja milyen hatással lehet. Ha nincs megadva fájlrendszer, akkor az alapértelmezett `default>user>trusted-service-user>demo_df`et használja a rendszer, ebben az esetben . Az adatok a megadott fájlrendszer helyére kerülnek.

    Figyeljük meg mind a "csv" és a "parketta" formátumban, írási műveletek egy könyvtár jön létre sok particionált fájlokat.

    ![A kimenet Tárolókezelő nézete](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage.png "A kimenet Tárolókezelő nézete")

    ![A kimenet Tárolókezelő nézete](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-default-storage2.png "A kimenet Tárolókezelő nézete")

## <a name="run-spark-sql-statements"></a>Spark SQL-utasítások futtatása

Az SQL az adatok lekérdezéséhez és meghatározásához leggyakrabban és legszélesebb körben használt nyelv. A Spark SQL az Apache Spark bővítményeként működik a strukturált adatok ismerős SQL-szintaxissal való feldolgozásához.

1. Illessze be a következő kódot egy üres cellába, majd futtassa a kódot. A parancs felsorolja a készleten lévő táblákat.

   ```sql
   %%sql
   SHOW TABLES
   ```

   Amikor egy jegyzetfüzetet használ a Synapse Analytics Apache Spark `sqlContext` készletével, egy előre beállított, amely segítségével lekérdezéseket futtathat a Spark SQL használatával. `%%sql`megmondja a jegyzetfüzetnek, `sqlContext` hogy használja a készletet a lekérdezés futtatásához. A lekérdezés lekéri a felső 10 sort egy olyan rendszertáblából, amely alapértelmezés szerint az összes Synapse Analytics Apache Spark-készlethez tartozik.

2. Futtasson egy másik lekérdezést a `demo_df` adatainak megtekintéséhez.

    ```sql
    %%sql
    SELECT * FROM demo_df
    ```

    A kód két kimeneti cellát hoz létre, amelyek közül az egyik adateredményeket tartalmaz, a másikat, amely a feladatnézetet jeleníti meg.

    Alapértelmezés szerint az eredménynézet rácsot jelenít meg, de a rács alatt van egy nézetváltó, amely lehetővé teszi, hogy a nézet váltson a rács- és a diagramnézet között.

    ![Lekérdezési kimenet a Synapse Analytics Sparkban](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query.png "Lekérdezési kimenet a Synapse Analytics Sparkban")

3. A **Nézetváltóban** válassza a **Diagram** lehetőséget
4. Válassza a **Nézet beállításai** ikont a jobb szélen.
5. A **Diagram típus** mezőjében válassza a "sávdiagram" lehetőséget.
6. Az X tengely oszlopmezőjében válassza az "állapot" lehetőséget.
7. Az Y tengely oszlopmezőjében válassza a "fizetés" lehetőséget.
8. Az **Összesítés** mezőben válassza az "AVG" lehetőséget.
9. Kattintson az **Alkalmaz** gombra.

   ![Diagramkimenet a Synapse Analytics Sparkban](./media/apache-spark-notebook-create-spark-use-sql/spark-get-started-query-chart-output.png "Diagramkimenet a Synapse Analytics Sparkban")

10. Lehetőség van arra, hogy ugyanazt a tapasztalatot fut SQL, de anélkül, hogy váltani nyelvek. Ezt úgy teheti meg, hogy a fenti SQL cellát lecseréli erre a PySpark cellára, a kimeneti élmény ugyanaz, mert a **megjelenítési** parancsot használja:

    ```python
    display(spark.sql('SELECT * FROM demo_df'))
    ```

11. A korábban végrehajtott cellák mindegyike átszelődött az **Előzmények kiszolgálójára** és **a Figyelés szolgáltatásra.** A hivatkozásokra kattintva a felhasználói élmény különböző részeire léphet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A Synapse Analytics menti az adatokat az Azure Data Lake Storage-ban. Biztonságosan leállíthatja a Spark-példányt, ha nincs használatban. A Synapse Analytics Apache Spark-készletért díjat számítunk fel, amíg fut, még akkor is, ha nincs használatban. Mivel a készlet díjai sokszor több, mint a tárolási díjak, célszerű hagyni spark példányok leállítása, amikor nincsenek használatban.

Győződjön meg arról, hogy a Spark-példány le van állítva, állítsa le a csatlakoztatott munkamenetek (notebookok). A készlet leáll, amikor az Apache Spark-készletben megadott **tétlen idő** elérésekor leáll. A munkamenet **befejezését** a jegyzetfüzet alján lévő állapotsorból is kiválaszthatja.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Synapse Analytics Apache Spark-készletet, és futtathat egy alapvető Spark SQL-lekérdezést.

- [Azure Synapse Analytics](../overview-what-is.md)
- [Az Apache Spark dokumentációja .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Az Apache Spark hivatalos dokumentációja](https://spark.apache.org/docs/latest/)

>[!NOTE]
> A hivatalos Apache Spark-dokumentáció a Spark-konzol használatára támaszkodik, amely nem érhető el az Azure Synapse Sparkon. Használja inkább a [notebookot](../spark/apache-spark-notebook-create-spark-use-sql.md) vagy [az IntelliJ-élményeket.](../spark/intellij-tool-synapse.md)
