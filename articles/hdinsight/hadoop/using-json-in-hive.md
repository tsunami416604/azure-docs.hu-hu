---
title: Elemzése és feldolgozni a JSON-dokumentumok Apache Hive Azure hdinsight |} Microsoft Docs
description: Megtudhatja, hogyan használja a JSON-dokumentumok és apache Hive Azure HDInsight segítségével elemezheti őket
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: 04c3a8262e52a630012a0a70e4b1ccb0ade76449
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Feldolgozhatja és elemezheti a JSON-dokumentumokat az Azure HDInsight Apache Hive használatával

Megtudhatja, hogyan feldolgozhatja és elemezheti a JavaScript Object Notation (JSON) fájlok Apache Hive Azure hdinsight használatával. Ez az oktatóanyag a következő JSON-dokumentum használja:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

A fájl megtalálható **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Azure Blob storage és a HDInsight együttes használatáról további információk: [használata HDFS-kompatibilis Azure Blob storage hadooppal a Hdinsightban](../hdinsight-hadoop-use-blob-storage.md). Az alapértelmezett tároló, a fürt másolhat a fájlt.

Ebben az oktatóanyagban a Hive-konzol használata. Nyissa meg a Hive konzolt utasításokért lásd: [használata a távoli asztal hdinsight Hadoop Hive](apache-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>JSON-dokumentumok egybesimítására
A következő szakaszban felsorolt módszerek szükséges, hogy a JSON-dokumentum állhat egyetlen sort. Igen kell egybesimítására a JSON-dokumentum karakterláncra. Ha már egybesimított-e a JSON-dokumentumában, kihagyhatja ezt a lépést, és lépjen a következő szakaszban a JSON-adatok elemzése. A JSON-dokumentum egybesimítására, futtassa a következő parancsfájlt:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

A nyers JSON-fájl **wasb://processjson@hditutorialdata.blob.core.windows.net/**. A **StudentsRaw** Hive tábla pontok a nyers JSON-dokumentum nem egybesimított számára.

A **StudentsOneLine** Hive tábla tárolja az adatokat a HDInsight alapértelmezett fájlrendszer alatt a **/json/diákok/** elérési útja.

A **BESZÚRÁSA** utasítás tölti fel a **StudentOneLine** a egybesimított JSON-adatokat tartalmazó táblát.

A **válasszon** utasítás csak egy sort adja vissza.

Íme a kimenetét a **kiválasztása** utasítást:

![A JSON-dokumentum egybesimítását][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>A Hive JSON-dokumentumok elemzése
Hive nyújt három különböző mechanizmusokat, lekérdezéseket futtathat a JSON-dokumentumokat, vagy írhat a saját:

* A get_json_object felhasználói függvény (UDF) használja.
* Az UDF json_tuple használja.
* Az egyéni szerializáló/deszerializáló (SerDe) használja.
* A saját UDF írási Python vagy más nyelv használatával. A saját Python kód futtatásához a Hive további információkért lásd: [Python UDF Apache Hive és a Pig][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Az UDF get_json_object használata
Hive biztosít egy beépített UDF nevű [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) JSON lekérdezése során futásidejű végezheti. Ez a módszer két argumentummal--a tábla neve és a metódus nevét, amelynek a egybesimított JSON-dokumentum, és elemezni kell JSON mező. Nézzük például a UDF működése megjelenítéséhez.

A következő lekérdezés utó- és vezetéknevet minden student adja vissza:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Ez a lekérdezés futtatásakor a konzolablakban, Íme kimenete:

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Nincsenek a get_json_object UDF korlátozásai:

* A lekérdezés minden mezője használatához a lekérdezés reparsing, az hatással van a teljesítményre.
* **ELSŐ\_JSON_OBJECT()** tömb karakterlánc alakot adja vissza. A tömb konvertálása a Hive-tömb, kell használni a reguláris kifejezések lecseréli a kapcsos zárójeleket "[" és "]", és akkor is rendelkezik hívására felosztása a tárolótömb eléréséhez.

Ezért a Hive wiki json_tuple használatát javasolja.  

### <a name="use-the-jsontuple-udf"></a>Az UDF json_tuple használata
Egy másik UDF Hive által biztosított nevezik [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), melyik teljesít jobban, mint [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Ez a módszer szükséges kulcsokat és a JSON karakterláncnak, és adja vissza egy rekord értékek egyetlen funkció segítségével. A következő lekérdezés a JSON-dokumentum a student-Azonosítót és a besorolási adja vissza:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

A Hive-konzolon a parancsfájl kimenete:

![json_tuple UDF-ben][image-hdi-hivejson-jsontuple]

Az UDF-ben használt json_tuple a [nézet oldalirányú](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) szintaxis a Hive, amely lehetővé teszi a json\_rekordot virtuális tábla létrehozása az UDT függvény az eredeti táblázat minden egyes sorára alkalmazásával. Ismételt használata miatt túl kezelése nehézkessé válik összetett JSONs **OLDALIRÁNYÚ NÉZET**. Ezenkívül **JSON_TUPLE** beágyazott JSONs nem tudja kezelni.

### <a name="use-a-custom-serde"></a>Egy egyéni SerDe használata
SerDe a legjobb választás beágyazott JSON-dokumentumok elemzéséhez. Lehetővé teszi, hogy a JSON-séma határozza meg, és ezután használhatja a séma a dokumentumok elemzése. Útmutatásért lásd: [egyéni JSON-SerDe használata a Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Összegzés
Végezetül a JSON operátor szerepel a Hive, az Ön által függ a forgatókönyvéhez. Ha egy egyszerű JSON-dokumentum, és csak egy mezőt kereshető rendelkezik, ha szeretné használni a Hive UDF get_json_object. Ha egynél több kulcs keressük meg, majd használhatja json_tuple. Ha a beágyazott dokumentumok, akkor a JSON SerDe kell használnia.

## <a name="next-steps"></a>További lépések

Kapcsolódó cikkek lásd:

* [Apache log4j mintafájl elemzéséhez a HDInsight Hadoop Hive és a HiveQL használata](../hdinsight-use-hive.md)
* [Repülési késleltetés adatok elemzése a Hive HDInsight használatával](../hdinsight-analyze-flight-delay-data.md)
* [Twitter-adatok elemzése a Hive HDInsight használatával](../hdinsight-analyze-twitter-data.md)

[hdinsight-python]:python-udf-hdinsight.md

[image-hdi-hivejson-flatten]: ./media/using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png

