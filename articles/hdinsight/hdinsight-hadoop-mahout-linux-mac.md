<properties
    pageTitle="使用 Mahout 與以 Linux 為基礎的 HDInsight 產生推薦 | Microsoft Azure"
    description="了解如何搭配 Linux 架構的 HDInsight (Hadoop) 使用 Apache Mahout 機器學習庫來產生電影推薦。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/06/2015"
    ms.author="larryfr"/>


# 透過在 HDInsight 上將 Apache Mahout 與 Linux 架構的 Hadoop 搭配使用來產生電影推薦 (預覽)

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

了解如何使用 [Apache Mahout](http://mahout.apache.org) 機器學習庫產生電影推薦的 Azure hdinsight。

Mahout 是 [機器學習 ][ml] Apache hadoop 文件庫。 Mahout 包含可處理資料的演算法，例如篩選、分類和叢集化。 在本文中，您將使用推薦引擎，其將根據朋友看過的電影來產生電影推薦。
> [AZURE.NOTE] 本文件中的步驟要求 HDInsight 叢集上有 Linux 架構的 Hadoop (預覽)。 如需搭配使用 Mahout 與 Windows 叢集的資訊，請參閱 [Windows 為基礎的 Hadoop，在 HDInsight 中搭配使用 Apache Mahout 產生電影推薦](hdinsight-mahout.md)

## 必要條件

* HDInsight 叢集上以 Linux 為基礎的 Hadoop。 如需建立一個，請參閱 [開始使用以 Linux 為基礎的 Hdinsight 中 ][getstarted]

## Mahout 版本控制

如需詳細的 Mahout 隨附於 HDInsight 叢集版本的詳細資訊，請參閱 [HDInsight 版本和 Hadoop 元件](hdinsight-component-versioning.md)。
> [AZURE.WARNING] 雖然可以將 Mahout 的不同版本上傳至 HDInsight 叢集，但只有 HDInsight 叢集隨附的元件可獲得完整支援，且 Microsoft 支援服務將助您釐清及解決與這些元件相關的問題。
>
> 自訂元件則獲得商務上合理的支援，協助您進一步疑難排解問題。 如此可能會進而解決問題，或要求您利用可用管道，以找出開放原始碼技術，從中了解該技術的深度專業知識。 例如，有許多社群網站可以使用，像是: [適用於 HDInsight 的 MSDN 論壇](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), ，[http://stackoverflow.com](http://stackoverflow.com)。 Apache 專案也有專案網站 [http://apache.org](http://apache.org), ，例如: [Hadoop](http://hadoop.apache.org/), ，[Spark](http://spark.apache.org/)。

## <a name="recommendations"></a>了解推薦

Mahout 提供的其中一項功能是推薦引擎。 這個引擎接受資料的格式 `userID`, ，`itemId`, ，和 `prefValue` (使用者偏好的項目)。 Mahout 接著可以執行共生分析判斷出: 具有喜好設定項目 (_u) 也有偏好這些其他影片 _。 接著 Mahout 會以偏好的類似項目判斷使用者，並以此做出推薦。

以下使用電影做一個很簡單的範例：

* __共生__: Joe、 Alice 和 Bob 都喜歡 _ 星際大戰 _、 (_t) 帝國大反擊 _ 和 _ 絕地大反攻 _。 Mahout 將判斷喜歡上述任何一部電影的使用者，也會喜歡另外兩部電影。

* __共生__: Bob 和 Alice 也喜歡 _ 威脅潛伏 _、 _ 複製人全面進攻 _ 和 _ 西斯大帝的復仇。 Mahout 將判斷喜歡前三部電影的使用者，也會喜歡這三部電影。

* __相似性推薦__：因為 Joe 喜歡前三部電影，Mahout 會查看具有相似偏好的其他使用者所喜歡但 Joe 還沒看過 (喜歡/評價) 的電影。 在此情況下，Mahout 將會推薦 (_t) 威脅潛伏 _、 _ 複製人全面進攻 _ 和 _ 西斯大帝的復仇。

## 載入資料

為了方便起見， [GroupLens Research ][movielens] 提供與 Mahout 相容格式的電影評價資料。 使用下列步驟來下載資料，然後將其載入至叢集的預設儲存體：

1. 使用 SSH 來連線至 Linux 架構的 HDInsight 叢集。 連接時要使用的位址是 `CLUSTERNAME-ssh.azurehdinsight.net` 連接埠則是 `22`。

    如需有關使用 SSH 連線至 HDInsight 的詳細資訊，請參閱下列文件：

    * **Linux、 Unix 或 OS X 用戶端**: 請參閱 [連接至 Linux 架構的 HDInsight 叢集從 Linux、 OS X 或 Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Windows 用戶端**: 請參閱 [從 Windows 連接至以 Linux 為基礎的 HDInsight 叢集](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

2. 下載 MovieLens 100k 封存檔，其中包含 1000 位使用者針對 1700 部電影的 100,000 個評價。

        curl -O http://files.grouplens.org/datasets/movielens/ml-100k.zip

3. 使用下列命令將封存檔解壓縮：

        unzip ml-100k.zip

    這會將內容解壓縮至新的資料夾 **ml-100 k**。

4. 使用下列命令將資料複製到 HDInsight 儲存體：

        cd ml-100k
        hdfs dfs -put u.data /example/data

    此檔案中所包含的資料具有的結構 `userID`, ，`movieID`, ，`userRating`, ，和 `時間戳記`, ，可告訴我們如何每位使用者電影的評價。 以下是資料範例：

        196 242 3   881250949
        186 302 3   891717742
        22  377 1   878887116
        244 51  2   880606923
        166 346 1   886397596


## 執行工作

使用下列命令來執行推薦工作：

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /example/data/u.data -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [AZURE.NOTE] 此工作可能需要幾分鐘的時間才能完成，並可能執行多項 MapReduce 工作。

## 檢視輸出

1. 工作完成後，使用以下命令來檢視所產生的輸出：

        hdfs dfs -text /example/data/mahoutout/part-r-00000

    輸出將如下所示：

        1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    第一欄是 `userID`。 中包含的值 '[' 和']' 是 `movieId`:`recommendationScore`。

2. **ml-100k** 目錄內含的其他一些資料可用來讓資料更方便使用者使用。 首先，使用下列命令下載資料：

        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt

    這會將輸出資料複製到目前目錄中名為 **recommendations.txt** 的檔案。

3. 使用下列命令來建立新的 Python 指令碼，該指令碼將會在建議輸出資料中查閱電影名稱：

     nano show_recommendations.py

 開啟編輯器時，請使用下列做為檔案的內容：

     #!/usr/bin/env python
    
     import sys
    
     if len(sys.argv) != 5:
             print "Arguments: userId userDataFilename movieFilename recommendationFilename"
             sys.exit(1)
    
     userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]
    
     print "Reading Movies Descriptions"
     movieFile = open(movieFilename)
     movieById = {}
     for line in movieFile:
             tokens = line.split("|")
             movieById[tokens[0]] = tokens[1:]
     movieFile.close()
    
     print "Reading Rated Movies"
     userDataFile = open(userDataFilename)
     ratedMovieIds = []
     for line in userDataFile:
             tokens = line.split("\t")
             if tokens[0] == userId:
                     ratedMovieIds.append((tokens[1],tokens[2]))
     userDataFile.close()
    
     print "Reading Recommendations"
     recommendationFile = open(recommendationFilename)
     recommendations = []
     for line in recommendationFile:
             tokens = line.split("\t")
             if tokens[0] == userId:
                     movieIdAndScores = tokens[1].strip("[]\n").split(",")
                     recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
                     break
     recommendationFile.close()
    
     print "Rated Movies"
     print "------------------------"
     for movieId, rating in ratedMovieIds:
             print "%s, rating=%s" % (movieById[movieId][0], rating)
     print "------------------------"
    
     print "Recommended Movies"
     print "------------------------"
     for movieId, score in recommendations:
             print "%s, score=%s" % (movieById[movieId][0], score)
     print "------------------------"

 按下 **CTRL-X**、**Y**，最後再按 **Enter** 鍵儲存資料。

3. 使用下列命令，讓檔案可以執行：

        chmod +x show_recommendations.py

4. 執行 Python 指令碼。 下列會假設您已在 ml-100k 目錄中，其中 `u.data` 和 `u.item` 檔案的位置:

        ./show_recommendations.py 4 u.data u.item recommendations.txt

    這要看看為使用者 ID 4 所產生的建議。

    * **u.data** 檔案用來擷取使用者已評分的電影
    * **u.item** 檔案用來擷取電影名稱
    * **recommendations.txt** 用來擷取這位使用者的電影建議

    此命令的輸出會如下所示：

        讀取電影描述
        讀取分級的電影
        建議閱讀
        評分的電影
        ------------------------
        模擬 (1997)，分級 = 3
        Ulee 的黃金 (1997 年) 評等 = 5
        Incognito (1997 年) 評等 = 5
        一飛透過 Cuckoo 的巢狀 (1975)、 評等 = 4
        事件水平 (1997)、 評等 = 4
        用戶端，(1994) 評等 = 3
        分級 liar Liar (1997 年) = 5
        Scream (1996 年)、 評等 = 4
        星際大戰 》 (1977)、 評等 = 5
        婚禮歌手，(1998) 評等 = 5
        分級迷航 Troopers (1997 年) = 4
        空軍一個 (1997)、 評等 = 5
        陰謀理論 (1997)、 評等 = 3
        請連絡 (1997)、 評等 = 5
        印第安納 Jones 和最後一個聖戰 (1989)、 評等 = 3
        稍微 (1998)，分級 = 5
        七個 (Se7en) (1995 年) 評等 = 4
        察土地 (1997)、 評等 = 5
        遺失的高速公路 (1997 年) 評等 = 5
        指派、 (1997 年) 評等 = 5
        藍調兄弟 2000 (1998)，分級 = 5
        評等: 繁衍 (1997 年) = 2
        夢中仙境 (1997)、 評等 = 5
        中 (& o) (1997 年) 評等 = 5
        ------------------------
        建議的影片
        ------------------------
        Tibet (1997 年) 中的七年得分 = 5.0
        印第安納 Jones 和最後一個聖戰 (1989)、 得分 = 5.0
        Jaws (1975)、 得分 = 5.0
        偵測以及 Sensibility (1995)，分數 = 5.0
        評分獨立性天 (ID4) (1996 年) = 5.0
        我最好的朋友婚禮 (1997)、 得分 = 5.0
        Jerry Maguire (1996 年) 分數 = 5.0
        Scream 2 (1997 年) 分數 = 5.0
        (1996 年) 的時間來終止、 得分 = 5.0
        石，(1996)、 得分 = 5.0
        ------------------------

## 刪除暫存資料

Mahout 工作不會移除處理工作時所建立的暫存資料。  `-TempDir` 暫存檔隔離到特定路徑中以方便刪除範例工作中指定參數。 若要移除暫存檔案，請使用下列命令：

    hdfs dfs -rm -f -r /temp/mahouttemp

> [AZURE.WARNING] 如果您要再次執行該命令，您必須也刪除輸出目錄。 使用以下命令刪除此目錄：
>
> `hdfs dfs-rm-f-r /example/data/mahoutout`

## 後續步驟

您現在已了解如何使用 Mahout，請繼續探索在 HDInsight 上使用資料的其他方法：

* [搭配 HDInsight 使用 hive](hadoop-use-hive.md)
* [搭配 HDInsight 使用 pig](hadoop-use-pig.md)
* [搭配 HDInsight 使用 MapReduce](hadoop-use-mapreduce.md)


[build]: http://mahout.apache.org/developers/buildingmahout.html 
[movielens]: http://grouplens.org/datasets/movielens/ 
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip 
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md 
[upload]: hdinsight-upload-data.md 
[ml]: http://en.wikipedia.org/wiki/Machine_learning 
[forest]: http://en.wikipedia.org/wiki/Random_forest 
[management]: https://manage.windowsazure.com/ 
[enableremote]: ./media/hdinsight-mahout/enableremote.png 
[connect]: ./media/hdinsight-mahout/connect.png 
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png 
[tools]: https://github.com/Blackmist/hdinsight-tools 

