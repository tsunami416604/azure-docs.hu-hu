---
title: Apache Base-lekérdezések futtatása az Azure HDInsightban az Apache Phoenix segítségével
description: Ismerje meg, hogyan futtathat Apache Zeppelin használatával Apache Base-lekérdezéseket a Phoenix segítségével.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392240"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Apache Zeppelin használatával apache phoenix-lekérdezések futtatása apache HBase-en keresztül az Azure HDInsightban

Az Apache Phoenix egy nyílt forráskódú, masszívan párhuzamos relációs adatbázis-réteg, amely a HBase-re épül. A Phoenix lehetővé teszi az SQL-mint lekérdezések használatát a HBase-en keresztül. A Phoenix az alatta lévő JDBC-illesztőprogramokat használja az SQL-táblák, indexek, nézetek és sorozatok létrehozásához, törléséhez, módosításához.  A Phoenix segítségével külön-külön és ömlesztve is frissítheti a sorokat. A Phoenix a MapReduce használata helyett NOSQL natív összeállítást használ a lekérdezések fordításához, lehetővé téve az alacsony késleltetésű alkalmazások létrehozását a HBase tetején.

Az Apache Zeppelin egy nyílt forráskódú webalapú noteszgép, amely lehetővé teszi, hogy interaktív adatelemzéssel és olyan nyelvekkel, mint az SQL és a Scala segítségével adatvezérelt, együttműködésen alapuló dokumentumokat hozzon létre. Segíti az adatfejlesztőket & adatszakértőknek az adatkezeléshez szükséges kód kidolgozásában, rendszerezésén, végrehajtásában és megosztásában. Lehetővé teszi az eredmények megjelenítését anélkül, hogy a parancssorra hivatkozna, vagy a fürt részleteire lenne szüksége.

A HDInsight-felhasználók az Apache Zeppelin segítségével lekérdezhetik a Phoenix-táblákat. Az Apache Zeppelin integrálva van a HDInsight-fürttel, és nincs további lépés a használatához. Egyszerűen hozzon létre egy Zeppelin notebook JDBC értelmező vel, és kezdje el írni a Phoenix SQL lekérdezéseket

## <a name="prerequisites"></a>Előfeltételek

Apache HBase-fürt a HDInsighton. Lásd: [Első lépések az Apache HBase-lel.](./apache-hbase-tutorial-get-started-linux.md)

## <a name="create-an-apache-zeppelin-note"></a>Apache Zeppelin note létrehozása

1. Cserélje `CLUSTERNAME` le a fürt nevét a `https://CLUSTERNAME.azurehdinsight.net/zeppelin`következő URL-címen . Ezután írja be az URL-címet egy webböngészőben. Adja meg a fürt bejelentkezési felhasználónevét és jelszavát.

1. A Zeppelin lapon válassza **az Új jegyzet létrehozása**lehetőséget.

    ![HDInsight interaktív lekérdezés zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. Az **Új jegyzet létrehozása** párbeszédpanelen írja be vagy jelölje ki a következő értékeket:

    - Megjegyzés neve: Adja meg a jegyzet nevét.
    - Alapértelmezett értelmező: Válassza ki a **jdbc** elemet a legördülő listából.

    Ezután válassza **a Jegyzet létrehozása**lehetőséget.

1. Győződjön meg arról, hogy a jegyzetfüzet fejlécében látható a csatlakoztatott állapot. Egy zöld pont jelöli a jobb felső sarokban.

    ![Zeppelin notebook állapota](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin notebook állapota")

1. Hozzon létre egy HBase táblát. Írja be a következő parancsot, majd nyomja **le a Shift + Enter billentyűkombinációt:**

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    Az első sorban lévő **%jdbc(phoenix)** utasítás arra utasítja a jegyzetfüzetet, hogy használja a Phoenix JDBC értelmezőt.

1. Létrehozott táblák megtekintése.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Értékek beszúrása a táblázatba.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. A tábla lekérdezése.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Rekord törlése.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Dobd el az asztalt.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>További lépések

- [Az Apache Phoenix mostantól támogatja a Zeppelint az Azure HDInsightban](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Apache Phoenix nyelvtan](https://phoenix.apache.org/language/index.html)
