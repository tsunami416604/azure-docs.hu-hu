<properties
   pageTitle="利用 HDInsight 中的 Hive 分析和處理 JSON 文件 |Microsoft Azure"
   description="了解如何使用 JSON 文件，以集使用 HDInsight 中的 Hive 分析它們。"
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>



# 使用 HDInsight 中的 Hive 處理並分析 JSON 文件

了解如何使用 HDInsight 中的 Hive 處理並分析 JSON 文件。 本教學課程中將使用下列 JSON 文件

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

如果您想要的話，可以將檔案複製到叢集的預設容器。

在本教學課程中，您將使用 Hive 主控台。

## 簡維 JSON 文件

下一節所列的方法需要 JSON 文件在單一資料列中。 因此，您必須將 JSON 文件簡維成一個字串。 如果已簡維 JSON 文件，您就可以略過此步驟，直接進入與分析 JSON 資料相關的下一節。

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

*StudentsRaw* Hive 資料表指向原始未簡維的 JSON 文件。

*StudentsOneLine* Hive 資料表會將資料儲存在 HDInsight 預設檔案系統的 */json/students/* 路徑下。

INSERT 陳述式會簡維後的 JSON 資料填入 StudentOneLine 資料表。

SELECT 陳述式應該只會傳回 1 個資料列。

以下是 SELECT 陳述式的輸出：

![JSON 文件簡維。][image-hdi-hivejson-flatten]

## 在 Hive 中分析 JSON 文件

Hive 提供三種不同的機制，可在 JSON 文件上執行查詢：

- 
- 使用 JSON_TUPLE UDF
- 使用自訂 SerDe
- 使用 Python 或其他語言撰寫您自己的 UDF。

### 

此方法採用兩個引數 – 資料表名稱和方法名稱，後者具有簡維 JSON 文件和必須剖析的 JSON 欄位。 讓我們看看此 UDF 如何運作的範例。

取得每個學生的姓氏與名字

    SELECT 
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'), 
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName') 
    FROM StudentsOneLine;

以下是在主控台視窗中執行此查詢時的輸出。

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

get-json_object UDF 有幾項限制。

- 因為查詢中的每個欄位都需要重新剖析查詢，所以它會影響效能。
- 若要將其轉換成 Hive 陣列，您必須使用規則運算式來取代方括號 ‘[‘ 和 ‘]’，然後呼叫分割以取得陣列。


這就是 Hive wiki 建議使用 json_tuple 的原因。

### 使用 JSON_TUPLE UDF

這個方法會採用一組索引鍵和 JSON 字串，並使用一個函式傳回值的 tuple。 下列查詢會從 JSON 文件傳回學生識別碼以及年級：

    SELECT q1.StudentId, q1.Grade 
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

這個指令碼在 Hive 主控台的輸出：

![json_tuple UDF][image-hdi-hivejson-jsontuple]

複雜 JSON 會重複使用橫向檢視，因此變得難以使用。 此外，JSON_TUPLE 無法處理巢狀 JSON。


### 使用自訂 SerDe

SerDe 是用來剖析巢狀 JSON 文件的最佳選擇，它可讓您定義 JSON 結構描述，並使用該結構描述來剖析文件。

**使用自訂 SerDe：**

1. 如果您要使用 HDInsight 的 Windows 部署，請選擇 Windows x64 版本的 JDK。
    >[AZURE.WARNING] JDK 1.8 不適用於此 SerDe。 

    安裝完成之後，請加入新的使用者環境變數：

    1. 從 Windows 畫面開啟 [**檢視進階系統設定**]。
    2. 按一下 [**環境變數**]。
    3. 加入新的 **JAVA_HOME** 環境變數，其指向 **C:\Program Files\Java\jdk1.7.0_55** 或任何安裝 JDK 的位置。

    ![為 JDK 設定正確的組態值][image-hdi-hivejson-jdk]

2. 

    

    ![設定 Maven][image-hdi-hivejson-maven]

3. 您可以按一下 [下載 Zip] 按鈕即可完成，如以下螢幕擷取畫面所示。

    ![複製專案][image-hdi-hivejson-serde]

這樣應該會建立您稍後會複製到叢集的必要 jar 檔案。

5：移至根資料夾 (您已將封裝下載至其中) 下的目標資料夾。 上傳 json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar 檔案到叢集的前端節點。 我通常會將它放在 hive 二進位資料夾底下：C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin 或類似位置。

6：在 hive 提示字元中，輸入 “add jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar”。 因為在我的案例中，jar 位於 C:\apps\dist\hive-0.13.x\bin 資料夾中，所以我可以直接以此名稱新增 jar，如下所示：

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;
    
    ![Adding JAR to your project][image-hdi-hivejson-addjar]

現在，您已準備好使用 SerDe 對 JSON 文件執行查詢。

下列陳述式會利用已定義的結構描述建立資料表

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string, 
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

列出學生的姓氏與名字

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

以下是 Hive 主控台的結果。

![SerDe 查詢 1][image-hdi-hivejson-serde_query1]

計算 JSON 文件的分數總和

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;



以下是 Hive 主控台的輸出。

![SerDe 查詢 2][image-hdi-hivejson-serde_query2]


    SELECT  
       
    
      



![SerDe 查詢 3][image-hdi-hivejson-serde_query3]



    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");

## 摘要

總而言之，您在 Hive 中選擇的 JSON 運算子類型取決於您的案例。  如果您有多個索引鍵需要查閱，則您可以使用 json_tuple。 如果您有巢狀文件，您應該使用 JSON SerDe。

如需其他相關文章，請參閱

- 
- 
- 
- 


[hdinsight-python]: hdinsight-python.md 
[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png 
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png 
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png 
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png 
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png 
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png 
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png 
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png 
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png 
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png 
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png 

