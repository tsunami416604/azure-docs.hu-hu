---
title: A HDInsight - Azure Hadoop Twitter adatok elemzése |} Microsoft Docs
description: Útmutató egy adott word használati gyakoriságának a HDInsight hadoop Twitter-adatok elemzése a Hive segítségével.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 78e4ea33-9714-424d-ac07-3d60ecaebf2e
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: d81f7889122bcf887676496a056df2148cdff6e9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
ms.locfileid: "31593712"
---
# <a name="analyze-twitter-data-using-hive-in-hdinsight"></a>Hdinsight Hive eszközzel Twitter-adatok elemzése
Közösségi webhelyek egyik fő növeli a big data alkalmazására vonatkozóan. Nyilvános API-k, például a Twitter helyek által biztosított az hasznos adatforrást ismertetése népszerű trendeket és elemzésére.
Ebben az oktatóanyagban meg fog Twitter-üzeneteket beolvasni egy Twitter-adatfolyam-API használatával, és használja Apache Hive on Azure HDInsight a Twitter-felhasználók, akik a legtöbb Twitter-üzeneteket, amelyek egy adott szó található küldött listáját.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy Windows-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement). Linux-alapú fürthöz adott lépései: [Hive HDInsight (Linux) használatával elemezheti Twitter adatok](hdinsight-analyze-twitter-data-linux.md).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* **A munkaállomás** az Azure PowerShell telepítése és konfigurálása.

    Windows PowerShell-parancsfájlok végrehajtása az Azure PowerShell futtassa rendszergazdaként és a végrehajtási házirend beállítása *RemoteSigned*. Lásd: [futtassa a Windows PowerShell-parancsfájlok][powershell-script].

    Windows PowerShell-parancsfájlok futtatásakor, mielőtt győződjön meg arról, hogy az Azure-előfizetéshez a következő parancsmag használatával csatlakozik:

    ```powershell
    Connect-AzureRmAccount
    ```

    Ha több Azure-előfizetéssel rendelkezik, a jelenlegi előfizetés beállításához használja a következő parancsmagot:

    ```powershell
    Select-AzureRmSubscription -SubscriptionID <Azure Subscription ID>
    ```

    > [!IMPORTANT]
    > A HDInsight-erőforrások Azure Service Managerrel történő kezelésének Azure PowerShell-támogatása **elavult**, így 2017. január 1-től megszűnt. A jelen dokumentumban leírt lépések az új HDInsight-parancsmagokat használják, amelyek az Azure Resource Managerrel működnek.
    >
    > Kérjük, kövesse az alábbi cikkben leírt lépéseket az Azure PowerShell legújabb verziójának telepítéséhez: [Install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Az Azure PowerShell letöltése és konfigurálása). Ha vannak olyan parancsprogramjai, amelyeket módosítani kell az új, az Azure Resource Managerrel működő parancsmagok használatához, tekintse meg az alábbi cikket: [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Az Azure Resource Manager-alapú fejlesztési eszközökre való áttérés HDInsight-fürtök esetén).

* **Egy Azure HDInsight fürt**. A fürtök kiépítése útmutatásért lásd: [használatának megkezdésében a HDInsight] [ hdinsight-get-started] vagy [Provision HDInsight clusters][hdinsight-provision]. Az oktatóanyag későbbi részében szüksége lesz a fürt nevét.

Az alábbi táblázat az ebben az oktatóanyagban használt fájlok:

| Fájlok | Leírás |
| --- | --- |
| /tutorials/Twitter/Data/tweets.txt |A Hive-feladatokban a forrásadatokban. |
| /tutorials/Twitter/output |A Hive-feladat kimeneti mappa. A Hive feladat kimeneti fájl alapértelmezett neve a **000000_0**. |
| tutorials/Twitter/Twitter.hql |A HiveQL-parancsfájlt. |
| /tutorials/Twitter/JobStatus |A Hadoop-feladat állapotát. |

## <a name="get-twitter-feed"></a>Get Twitter-hírcsatorna
Ebben az oktatóanyagban fogja használni a [streamelési API-k Twitter][twitter-streaming-api]. Az adott adatfolyam-használandó API Twitter van [állapotok-vagy szűrőkezelő][twitter-statuses-filter].

> [!NOTE]
> 10 000 Twitter-üzeneteket tartalmazó fájlt, és a Hive parancsfájl (a következő szakaszban ismertetett) fel lett töltve, egy nyilvános Blob tárolóban. Ez a szakasz kihagyhatja, ha szeretné használni a feltöltött fájlok.

[Twitter-üzeneteket adatok](https://dev.twitter.com/docs/platform-objects/tweets) összetett beágyazott szerkezetet tartalmaz JavaScript Object Notation (JSON) formátumban tárolja. Ahelyett, hogy hány sornyi kód írása a hagyományos programozási nyelv használatával, alakíthatja át a beágyazott struktúra egy Hive táblába úgy, hogy azt egy Structured Query Language (SQL) lekérdezhetők – például a HiveQL nevű nyelv.

Twitter OAuth használ meghatalmazott hozzáférést biztosítson az API-hoz. OAuth olyan hitelesítési protokoll, amely lehetővé teszi a felhasználók jóváhagyása nélkül megosztása a jelszavát a nevében eljárni alkalmazások. További információ található [oauth.net](http://oauth.net/) vagy a kiváló [Alapszintű útmutató az OAuth](http://hueniverse.com/oauth/) a Hueniverse.

Az első lépés lehetővé teszi az OAuth-hoz egy új alkalmazás létrehozása a fejlesztői Twitter-helyen.

**Twitter-alkalmazás létrehozása**

1. Jelentkezzen be [ https://apps.twitter.com/ ](https://apps.twitter.com/). Kattintson a **feliratkozás most** hivatkozásra, ha egy Twitter-fiók nem rendelkezik.
2. Kattintson a **új alkalmazás létrehozása**.
3. Adja meg **neve**, **leírás**, **webhely**. Hogy fel egy URL-címet a **webhely** mező. A következő táblázatban néhány példa értékeket:

   | Mező | Érték |
   | --- | --- |
   |  Name (Név) |MyHDInsightApp |
   |  Leírás |MyHDInsightApp |
   |  Honlap |http://www.myhdinsightapp.com |
4. Ellenőrizze **Igen, elfogadom**, és kattintson a **az Twitter-alkalmazás létrehozása**.
5. Kattintson a **engedélyek** fülre. Az alapértelmezett engedély **csak olvasható**. Ez a megfelelő ehhez az oktatóanyaghoz.
6. Kattintson a **kulcsok és a hozzáférési jogkivonatok** fülre.
7. Kattintson a **a hozzáférési jogkivonat létrehozása**.
8. Kattintson a **teszt OAuth** a lap jobb felső sarkában.
9. Írja le **kulcsa**, **felhasználói titok**, **hozzáférési jogkivonat**, és **Access token titkos**. Az oktatóanyag későbbi részében szüksége lesz az értékeket.

Ebben az oktatóanyagban Windows PowerShell használatával ellenőrizze a webes szolgáltatás hívása. A webszolgáltatás-hívások egyéb népszerű eszköz [ *Curl*][curl]. Curl letölthető [Itt][curl-download].

> [!NOTE]
> Használatakor a curl-parancsot a Windows idézőjelek közé foglalt helyett használjon szimpla idézőjelben az beállítási értékeit.

**Twitter-üzeneteket beolvasni**

1. Nyissa meg a Windows PowerShell integrált parancsfájlkezelési környezet (ISE). (A Windows 8 kezdőképernyőn írja be a **PowerShell_ISE** majd **Windows PowerShell ISE**. Lásd: [indítsa el a Windows PowerShell használatával Windows 8 és Windows][powershell-start].)
2. Másolja a következő a parancsfájl ablaktáblára:

    ```powershell
    #region - variables and constants
    $clusterName = "<HDInsightClusterName>" # Enter the HDInsight cluster name

    # Enter the OAuth information for your Twitter application
    $oauth_consumer_key = "<TwitterAppConsumerKey>";
    $oauth_consumer_secret = "<TwitterAppConsumerSecret>";
    $oauth_token = "<TwitterAppAccessToken>";
    $oauth_token_secret = "<TwitterAppAccessTokenSecret>";

    $destBlobName = "tutorials/twitter/data/tweets.txt" # This script saves the tweets into this blob.

    $trackString = "Azure, Cloud, HDInsight" # This script gets the tweets containing these keywords.
    $track = [System.Uri]::EscapeDataString($trackString);
    $lineMax = 10000  # The script will get this number of tweets. It is about 3 minutes every 100 lines.
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    Connect-AzureRmAccount
    #endregion

    #region - Create a block blob object for writing tweets into Blob storage
    Write-Host "Get the default storage account name and Blob container name using the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -Name $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $storageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $containerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $storageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $containerName." -ForegroundColor Yellow

    Write-Host "Define the Azure storage connection string ..." -ForegroundColor Green
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"
    Write-Host "`tThe connection string is $storageConnectionString." -ForegroundColor Yellow

    Write-Host "Create block blob object ..." -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($containerName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    #end region

    # region - Format OAuth strings
    Write-Host "Format oauth strings ..." -ForegroundColor Green
    $oauth_nonce = [System.Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes([System.DateTime]::Now.Ticks.ToString()));
    $ts = [System.DateTime]::UtcNow - [System.DateTime]::ParseExact("01/01/1970", "dd/MM/yyyy", $null)
    $oauth_timestamp = [System.Convert]::ToInt64($ts.TotalSeconds).ToString();

    $signature = "POST&";
    $signature += [System.Uri]::EscapeDataString("https://stream.twitter.com/1.1/statuses/filter.json") + "&";
    $signature += [System.Uri]::EscapeDataString("oauth_consumer_key=" + $oauth_consumer_key + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_nonce=" + $oauth_nonce + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_signature_method=HMAC-SHA1&");
    $signature += [System.Uri]::EscapeDataString("oauth_timestamp=" + $oauth_timestamp + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_token=" + $oauth_token + "&");
    $signature += [System.Uri]::EscapeDataString("oauth_version=1.0&");
    $signature += [System.Uri]::EscapeDataString("track=" + $track);

    $signature_key = [System.Uri]::EscapeDataString($oauth_consumer_secret) + "&" + [System.Uri]::EscapeDataString($oauth_token_secret);

    $hmacsha1 = new-object System.Security.Cryptography.HMACSHA1;
    $hmacsha1.Key = [System.Text.Encoding]::ASCII.GetBytes($signature_key);
    $oauth_signature = [System.Convert]::ToBase64String($hmacsha1.ComputeHash([System.Text.Encoding]::ASCII.GetBytes($signature)));

    $oauth_authorization = 'OAuth ';
    $oauth_authorization += 'oauth_consumer_key="' + [System.Uri]::EscapeDataString($oauth_consumer_key) + '",';
    $oauth_authorization += 'oauth_nonce="' + [System.Uri]::EscapeDataString($oauth_nonce) + '",';
    $oauth_authorization += 'oauth_signature="' + [System.Uri]::EscapeDataString($oauth_signature) + '",';
    $oauth_authorization += 'oauth_signature_method="HMAC-SHA1",'
    $oauth_authorization += 'oauth_timestamp="' + [System.Uri]::EscapeDataString($oauth_timestamp) + '",'
    $oauth_authorization += 'oauth_token="' + [System.Uri]::EscapeDataString($oauth_token) + '",';
    $oauth_authorization += 'oauth_version="1.0"';

    $post_body = [System.Text.Encoding]::ASCII.GetBytes("track=" + $track);
    #endregion

    #region - Read tweets
    Write-Host "Create HTTP web request ..." -ForegroundColor Green
    [System.Net.HttpWebRequest] $request = [System.Net.WebRequest]::Create("https://stream.twitter.com/1.1/statuses/filter.json");
    $request.Method = "POST";
    $request.Headers.Add("Authorization", $oauth_authorization);
    $request.ContentType = "application/x-www-form-urlencoded";
    $body = $request.GetRequestStream();

    $body.write($post_body, 0, $post_body.length);
    $body.flush();
    $body.close();
    $response = $request.GetResponse() ;

    Write-Host "Start stream reading ..." -ForegroundColor Green

    Write-Host "Define a MemoryStream and a StreamWriter for writing ..." -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream

    $sReader = New-Object System.IO.StreamReader($response.GetResponseStream())

    $inrec = $sReader.ReadLine()
    $count = 0
    while (($inrec -ne $null) -and ($count -le $lineMax))
    {
        if ($inrec -ne "")
        {
            Write-Host "`n`t $count tweets received." -ForegroundColor Yellow

            $writeStream.WriteLine($inrec)
            $count ++
        }

        $inrec=$sReader.ReadLine()
    }
    #endregion

    #region - Write tweets to Blob storage
    Write-Host "Write to the destination blob ..." -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)

    $sReader.close()
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. Állítsa be az első 5-8 változókat a parancsfájl:

    Változó|Leírás
    ---|---
    $clusterName|Azt a nevet a HDInsight-fürt, ahol az alkalmazást futtatni szeretné.
    $oauth_consumer_key|Ez az a Twitter-alkalmazás **kulcsa** megírt korábban a Twitter-alkalmazás létrehozásakor.
    $oauth_consumer_secret|Ez az a Twitter-alkalmazás **felhasználói titok** korábban megírt.
    $oauth_token|Ez az a Twitter-alkalmazás **hozzáférési jogkivonat** korábban megírt.
    $oauth_token_secret|Ez az a Twitter-alkalmazás **access token titkos** korábban megírt.
    $destBlobName|Ez az a kimeneti blob neve. Az alapértelmezett érték **tutorials/twitter/data/tweets.txt**. Ha megváltoztatja az alapértelmezett érték, szüksége lesz a Windows PowerShell-parancsfájlok ennek megfelelően frissülnek.
    $trackString|A webszolgáltatás visszatér a következő kulcsszavak kapcsolódó Twitter-üzeneteket. Az alapértelmezett érték **Azure-felhő, HDInsight**. Ha megváltoztatja az alapértelmezett érték, ennek megfelelően frissíti a Windows PowerShell-parancsfájlokat.
    $lineMax|Az érték határozza meg, hány Twitter-üzeneteket, a parancsfájl olvasását. Három perc olvasási 100 Twitter-üzeneteket vesz igénybe. Megadhat egy nagyobb számot, de letöltése több ideig tart.

1. A szkriptek futtatásához nyomja le az **F5** billentyűt. Ha a probléma megoldásához, problémát tapasztal a sorok, és nyomja le az **F8**.
2. Ekkor megjelenik a "Complete!" a kimeneti végén. Piros hibaüzenet jelenik meg.

Egy érvényesítési eljárással ellenőrizheti a kimeneti fájl **/tutorials/twitter/data/tweets.txt**, a Azure Blob-tároló egy Azure Tártallózó vagy az Azure PowerShell használatával. A Windows PowerShell-parancsfájlpélda a fájlok listázása, lásd: [és a HDInsight együttes használata a Blob storage][hdinsight-storage-powershell].

## <a name="create-hiveql-script"></a>Hozzon létre HiveQL-parancsfájlt
Az Azure PowerShell használatával több HiveQL utasítást egy olyan időpontra, vagy egy parancsfájl fájlba a HiveQL utasítás csomag. Ebben az oktatóanyagban létrehoz egy HiveQL-parancsfájlt. A parancsfájl az Azure Blob storage fel kell tölteni. A következő szakaszban az Azure PowerShell használatával fog futni a parancsfájlt.

> [!NOTE]
> A Hive parancsfájl és 10 000 Twitter-üzeneteket tartalmazó fájl fel lett töltve egy nyilvános Blob tárolóban. Ez a szakasz kihagyhatja, ha szeretné használni a feltöltött fájlok.

A HiveQL-parancsfájlt kell elvégezni a következőket:

1. **Dobja el a tweets_raw táblát** abban az esetben, ha a tábla már létezik.
2. **A tweets_raw Hive tábla létrehozásához**. Adatait tartalmazza a ideiglenes Hive strukturált táblázat további kinyerési, átalakítási és betöltési (ETL) feldolgozása. A partíciókon információkért lásd: [Hive oktatóanyag][apache-hive-tutorial].
3. **Adatok betöltése** a forrásmappából, /tutorials/twitter/data. A beágyazott JSON formátumban nagy Twitter-üzeneteket adatkészlet most átalakítása ideiglenes Hive tábla struktúrába.
4. **A Twitter-üzeneteket table DROP** abban az esetben, ha a tábla már létezik.
5. **A Twitter-üzeneteket tábla létrehozása**. Mielőtt a Hive eszközzel kérdezheti Twitter-üzeneteket adatkészlet ellen, egy másik ETL-folyamat futtatásához szükséges. Az ETL-folyamat a "twitter_raw" táblázatban tárolt adatok részletes táblázat séma határozza meg.
6. **Felülírás táblázat beszúrása**. Az összetett Hive parancsfájl a Hadoop-fürt lesz indítsa hosszú MapReduce-feladatok készlete. Attól függően, hogy a DataSet adatkészlet és a fürt méretét Ez körülbelül 10 percig eltarthat.
7. **INSERT felülírása directory**. Lekérdezés futtatása, és a kimeneti fájlba adatkészlet. Ez a lekérdezés listáját adja vissza a Twitter felhasználók, akik a legtöbb Twitter-üzeneteket, amely a word tartalmazott küldött "Azure".

**A Hive parancsprogram létrehozásához, és töltse fel az Azure-bA**

1. Nyissa meg a Windows PowerShell ISE.
2. Másolja a következő a parancsfájl ablaktáblára:

    ```powershell
    #region - variables and constants
    $clusterName = "<Existing HDInsight Cluster Name>" # Enter your HDInsight cluster name
    $subscriptionID = "<Azure Subscription ID>"

    $sourceDataPath = "/tutorials/twitter/data"
    $outputPath = "/tutorials/twitter/output"
    $hqlScriptFile = "tutorials/twitter/twitter.hql"

    $hqlStatements = @"
    set hive.exec.dynamic.partition = true;
    set hive.exec.dynamic.partition.mode = nonstrict;

    DROP TABLE tweets_raw;
    CREATE EXTERNAL TABLE tweets_raw (
        json_response STRING
    )
    STORED AS TEXTFILE LOCATION '$sourceDataPath';

    DROP TABLE tweets;
    CREATE TABLE tweets
    (
        id BIGINT,
        created_at STRING,
        created_at_date STRING,
        created_at_year STRING,
        created_at_month STRING,
        created_at_day STRING,
        created_at_time STRING,
        in_reply_to_user_id_str STRING,
        text STRING,
        contributors STRING,
        retweeted STRING,
        truncated STRING,
        coordinates STRING,
        source STRING,
        retweet_count INT,
        url STRING,
        hashtags array<STRING>,
        user_mentions array<STRING>,
        first_hashtag STRING,
        first_user_mention STRING,
        screen_name STRING,
        name STRING,
        followers_count INT,
        listed_count INT,
        friends_count INT,
        lang STRING,
        user_location STRING,
        time_zone STRING,
        profile_image_url STRING,
        json_response STRING
    );

    FROM tweets_raw
    INSERT OVERWRITE TABLE tweets
    SELECT
        cast(get_json_object(json_response, '$.id_str') as BIGINT),
        get_json_object(json_response, '$.created_at'),
        concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
        substr (get_json_object(json_response, '$.created_at'),27,4)),
        substr (get_json_object(json_response, '$.created_at'),27,4),
        case substr (get_json_object(json_response, '$.created_at'),5,3)
            when "Jan" then "01"
            when "Feb" then "02"
            when "Mar" then "03"
            when "Apr" then "04"
            when "May" then "05"
            when "Jun" then "06"
            when "Jul" then "07"
            when "Aug" then "08"
            when "Sep" then "09"
            when "Oct" then "10"
            when "Nov" then "11"
            when "Dec" then "12" end,
        substr (get_json_object(json_response, '$.created_at'),9,2),
        substr (get_json_object(json_response, '$.created_at'),12,8),
        get_json_object(json_response, '$.in_reply_to_user_id_str'),
        get_json_object(json_response, '$.text'),
        get_json_object(json_response, '$.contributors'),
        get_json_object(json_response, '$.retweeted'),
        get_json_object(json_response, '$.truncated'),
        get_json_object(json_response, '$.coordinates'),
        get_json_object(json_response, '$.source'),
        cast (get_json_object(json_response, '$.retweet_count') as INT),
        get_json_object(json_response, '$.entities.display_url'),
        array(
            trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
            trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
        array(
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
            trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
        trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
        trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
        get_json_object(json_response, '$.user.screen_name'),
        get_json_object(json_response, '$.user.name'),
        cast (get_json_object(json_response, '$.user.followers_count') as INT),
        cast (get_json_object(json_response, '$.user.listed_count') as INT),
        cast (get_json_object(json_response, '$.user.friends_count') as INT),
        get_json_object(json_response, '$.user.lang'),
        get_json_object(json_response, '$.user.location'),
        get_json_object(json_response, '$.user.time_zone'),
        get_json_object(json_response, '$.user.profile_image_url'),
        json_response
    WHERE (length(json_response) > 500);

    INSERT OVERWRITE DIRECTORY '$outputPath'
    SELECT name, screen_name, count(1) as cc
        FROM tweets
        WHERE text like "%Azure%"
        GROUP BY name,screen_name
        ORDER BY cc DESC LIMIT 10;
    "@
    #endregion

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green

    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Connect-AzureRmAccount
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    #endregion

    #region - Create a block blob object for writing the Hive script file
    Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
    $myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $myCluster.ResourceGroup
    $defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $myCluster.DefaultStorageContainer
    Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
    Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

    Write-Host "Define the connection string ..." -ForegroundColor Green
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

    Write-Host "Create block blob objects referencing the hql script file" -ForegroundColor Green
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $hqlScriptBlob = $storageContainer.GetBlockBlobReference($hqlScriptFile)

    Write-Host "Define a MemoryStream and a StreamWriter for writing ... " -ForegroundColor Green
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    $writeStream.Writeline($hqlStatements)
    #endregion

    #region - Write the Hive script file to Blob storage
    Write-Host "Write to the destination blob ... " -ForegroundColor Green
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $hqlScriptBlob.UploadFromStream($memStream)
    #endregion

    Write-Host "Completed!" -ForegroundColor Green
    ```

3. A parancsfájl az első két változók megadása:

   | Változó | Leírás |
   | --- | --- |
   |  $clusterName |Írja be a HDInsight-fürt nevét, ahol szeretné az alkalmazás futtatásához. |
   |  $subscriptionID |Adja meg az Azure-előfizetés azonosítóját. |
   |  $sourceDataPath |Az Azure Blob tárolási helyét, ahol a Hive-lekérdezéseket a adatokat olvassa. Nem kell módosítani a változót. |
   |  $outputPath |A Hive-lekérdezések lesz a kimeneti eredmények Azure Blob tárolási helyét. Nem kell módosítani a változót. |
   |  $hqlScriptFile |A hely és a HiveQL parancsfájl a fájl nevét. Nem kell módosítani a változót. |
4. A szkriptek futtatásához nyomja le az **F5** billentyűt. Ha a probléma megoldásához, problémát tapasztal a sorok, és nyomja le az **F8**.
5. Ekkor megjelenik a "Complete!" a kimeneti végén. Piros hibaüzenet jelenik meg.

Egy érvényesítési eljárással ellenőrizheti a kimeneti fájl **/tutorials/twitter/twitter.hql**, a Azure Blob-tároló egy Azure Tártallózó vagy az Azure PowerShell használatával. A Windows PowerShell-parancsfájlpélda a fájlok listázása, lásd: [és a HDInsight együttes használata a Blob storage][hdinsight-storage-powershell].

## <a name="process-twitter-data-by-using-hive"></a>Twitter-adatok feldolgozása a Hive eszközzel
Befejezte a előkészítő munkát. Most a Hive-parancsfájl elindíthat és az eredményeket.

### <a name="submit-a-hive-job"></a>Hive feladat elküldése
A következő Windows PowerShell-parancsfájl segítségével a Hive-parancsfájl futtatása. Szüksége lesz, ha az első változót.

> [!NOTE]
> A Twitter-üzeneteket és a feltöltött HiveQL-parancsfájlt az utolsó két szakaszban, állítsa $hqlScriptFile "/ tutorials/twitter/twitter.hql". A gazdarendszerhez meg lettek feltöltve egy nyilvános blob, állítsa $hqlScriptFile "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql".

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"
$httpUserName = "admin"
$httpUserPassword = "<HDInsight Cluster HTTP User Password>"

#use one of the following
$hqlScriptFile = "wasb://twittertrend@hditutorialdata.blob.core.windows.net/twitter.hql"
$hqlScriptFile = "/tutorials/twitter/twitter.hql"

$statusFolder = "/tutorials/twitter/jobstatus"
#endregion

$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value

$defaultBlobContainerName = $myCluster.DefaultStorageContainer

#region - Invoke Hive
Write-Host "Invoke Hive ... " -ForegroundColor Green

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpUserPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

Use-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName -HttpCredential $httpCredential
$response = Invoke-AzureRmHDInsightHiveJob -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -file $hqlScriptFile -StatusFolder $statusFolder #-OutVariable $outVariable

Write-Host "Display the standard error log ... " -ForegroundColor Green
$jobID = ($response | Select-String job_ | Select-Object -First 1) -replace ‘\s*$’ -replace ‘.*\s’
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $jobID -DefaultContainer $defaultBlobContainerName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -HttpCredential $httpCredential
#endregion
```

### <a name="check-the-results"></a>Az eredményeket
A következő Windows PowerShell-parancsfájl használatával ellenőrizze a Hive-feladat kimeneti. Szüksége lesz az első két változók megadása.

```powershell
#region variables and constants
$clusterName = "<Existing Azure HDInsight Cluster Name>"

$blob = "tutorials/twitter/output/000000_0" # The name of the blob to be downloaded.
#endregion

#region - Create an Azure storage context object
Write-Host "Get the default storage account name and container name based on the cluster name ..." -ForegroundColor Green
$myCluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $myCluster.ResourceGroup
$defaultStorageAccountName = $myCluster.DefaultStorageAccount.Replace(".blob.core.windows.net", "")
$defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
$defaultBlobContainerName = $myCluster.DefaultStorageContainer

Write-Host "`tThe storage account name is $defaultStorageAccountName." -ForegroundColor Yellow
Write-Host "`tThe blob container name is $defaultBlobContainerName." -ForegroundColor Yellow

Write-Host "Create a context object ... " -ForegroundColor Green
$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
#endregion

#region - Download blob and display blob
Write-Host "Download the blob ..." -ForegroundColor Green
cd $HOME
Get-AzureStorageBlobContent -Container $defaultBlobContainerName -Blob $blob -Context $storageContext -Force

Write-Host "Display the output ..." -ForegroundColor Green
Write-Host "==================================" -ForegroundColor Green
cat "./$blob"
Write-Host "==================================" -ForegroundColor Green
#end region
```

> [!NOTE]
> A Hive tábla \001 használja, mint a mezőhatárolóval. Az elválasztó karaktere nem látható, a kimenetben.

Miután az elemzés eredményeinek az Azure Blob storage lettek helyezve, exportálja az adatokat az Azure SQL adatbázis vagy SQL server, az adatok exportálása az Excel Power Query használatával vagy a Hive ODBC-illesztő segítségével csatlakozzon az alkalmazás számára az adatok. További információkért lásd: [és a HDInsight együttes használata Sqoop][hdinsight-use-sqoop], [adatelemzés repülési késleltetés HDInsight eszközzel][hdinsight-analyze-flight-delay-data], [HDInsight a Power Query az Excel csatlakozás][hdinsight-power-query], és [csatlakozzon az Excel a Microsoft Hive ODBC-illesztőprogram HDInsight][hdinsight-hive-odbc].

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban úgy találtuk, hogyan kell egy strukturálatlan JSON adatkészlet átalakítása strukturált Hive tábla lekérdezésére, vizsgálatát, és a Twitter adatok elemzése az Azure-on HDInsight használatával. További tudnivalókért lásd:

* [Első lépései a hdinsight eszközzel][hdinsight-get-started]
* [HDInsight eszközzel repülési késleltetés adatok elemzése][hdinsight-analyze-flight-delay-data]
* [Csatlakoztathatja az Excelt a HDInsight a Power Query][hdinsight-power-query]
* [Excel csatlakoztatása a Microsoft Hive ODBC-illesztőprogram HDInsight][hdinsight-hive-odbc]
* [Use Sqoop with HDInsight][hdinsight-use-sqoop]

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176961.aspx

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]:hdinsight-hadoop-use-blob-storage.md#access-blobs-using-azure-powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-power-query]:hadoop/apache-hadoop-connect-excel-power-query.md
[hdinsight-hive-odbc]:hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md
