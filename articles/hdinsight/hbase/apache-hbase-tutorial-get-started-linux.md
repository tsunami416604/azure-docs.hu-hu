---
title: Az Azure HDInsight használata az Apache HBase-oktatóanyag –
description: Ez az oktatóanyag az Apache HBase a HDInsight hadoop használatának megkezdéséhez. Táblákat hozhat létre a HBase rendszehéjból, és lekérdezheti azokat a Hive eszközzel.
keywords: hbasecommand,hbase példa
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: hrasheed
ms.openlocfilehash: 48b02a042b55af9ff65f57220f7a64c9cbde8848
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445542"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Oktatóanyag: Az Azure HDInsight az Apache HBase használata

Ez az oktatóanyag bemutatja, hogyan lehet Apache HBase-fürt létrehozása az Azure HDInsight, hozzon létre HBase-táblákat és lekérdezéstáblákat Apache Hive.  Általános HBase információért lásd: [HDInsight HBase overview](./apache-hbase-overview.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Apache HBase-fürt létrehozása
> * A HBase táblákat hozhat létre és adatok beszúrása
> * Az Apache Hive használata az Apache HBase lekérdezéséhez
> * HBase REST API-k használata Curl használatával
> * A fürt állapotának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

* A bash. Ebben a cikkben szereplő példák a Windows 10-es a bash használata a curl-parancs. Lásd: [Linux telepítési útmutató a Windows 10-es Windows-alrendszer](https://docs.microsoft.com/windows/wsl/install-win10) a telepítési lépéseket.  Más [Unix parancskörnyezet](https://www.gnu.org/software/bash/) is működnek.  A curl-példák, néhány kisebb módosításokkal, egy Windows parancssorban is működik.  Másik lehetőségként használhatja a Windows PowerShell-parancsmag [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Az Apache HBase-fürt létrehozása

A következő eljárás Azure Resource Manager-sablon hozhat létre HBase-fürt és a függő Azure Storage-fiókot használja. Az eljárásban és egyéb fürtlétrehozási módszerekben használt paraméterek megértéséhez lásd: [Create Linux-based Hadoop clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban).

1. Válassza ki az alábbi képre kattintva megnyithatja a sablont az Azure Portalon. A sablonban található [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Az **Egyéni üzembe helyezés** panelen adja meg a következő értékeket:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|Válassza ki az Azure-előfizetés, amely a fürt létrehozására szolgál.|
    |Erőforráscsoport|Hozzon létre egy Azure-erőforrás felügyeleti csoportot, vagy használjon egy meglévőt.|
    |Location|Adja meg az erőforráscsoport helyét. |
    |ClusterName|Adjon meg egy nevet a HBase-fürt.|
    |Fürt bejelentkezési nevet és jelszót|Az alapértelmezett bejelentkezési név az **admin**.|
    |SSH-felhasználónév és jelszó|Az alapértelmezett felhasználónév az **sshuser**.|

    Más paraméterek opcionálisak.  

    Minden egyes fürt az Azure Storage-fióktól függ. A fürtök törlése után az adatok megmaradnak a tárfiókban. A fürt alapértelmezett tárfiókneve a fürt neve a „store” kifejezéssel kiegészítve. A név szoftveresen kötött a sablonváltozók szakaszban.

3. Válassza ki **elfogadom a feltételeket és a fenti feltételeket**, majd válassza ki **beszerzési**. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

A HBase-fürtök törlése után egy másik HBase-fürtöt hozhat létre ugyanazon alapértelmezett blobtárolóval. Az új fürt felveszi az eredeti fürtben létrehozott HBase-táblákat. Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat.

## <a name="create-tables-and-insert-data"></a>Táblák létrehozása és adatok beszúrása

Ön SSH-val HBase-fürtökhöz csatlakozhat, és ezután [Apache HBase rendszerhéj](https://hbase.apache.org/0.94/book/shell.html) hozhat létre HBase-táblákat, helyezze be az adatokat, és adatokat kérdezhet le.

A legtöbbek számára az adatok táblázatos formátumban jelennek meg:

![HDInsight HBase táblázatos adatok](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

A HBase (megvalósítását [felhőalapú BigTable](https://cloud.google.com/bigtable/)), ugyanazokat az adatokat a következőhöz hasonló:

![HDInsight HBase BigTable-adatok](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**A Hbase-rendszerhéj használata**

1. Használat `ssh` paranccsal csatlakozhat a HBase-fürtöt. Az alábbi parancsot szerkesztése lecserélésével `CLUSTERNAME` a fürt nevét és adja meg a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Használat `hbase shell` parancsot a HBase interaktív shell elindításához. Adja meg az SSH-kapcsolatot a következő parancsot:

    ```bash
    hbase shell
    ```

1. Használat `create` egy HBase tábla létrehozásához két oszlopcsaláddal parancsot. A tábla- és oszlopneveket-és nagybetűk. Írja be a következő parancsot:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Használat `list` parancs minden, a HBase táblák listázásához. Írja be a következő parancsot:

    ```hbase
    list
    ```

1. Használat `put` értékek beszúrása egy megadott oszlop, egy adott tábla egy megadott sorának a következő parancsot. A következő parancsokat:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Használati `scan` parancs vizsgálata, és adja vissza a `Contacts` tábla adatait. Írja be a következő parancsot:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Hadoop HBase-rendszerhéj](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Használat `get` parancsot egy sor tartalmának beolvasása. Írja be a következő parancsot:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Hasonló eredményeket használja, tekintse meg a `scan` parancsot, mert csak egy sor van.

    A HBase táblasémáról további információért lásd: [Bevezetés az Apache HBase Schema Design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). További HBase parancsokért lásd: [Apache HBase referencia-útmutató](https://hbase.apache.org/book.html#quickstart).

1. Használat `exit` parancs használatával állítsa le a HBase interaktív kezelőfelület. Írja be a következő parancsot:

    ```hbaseshell
    exit
    ```

**Adatok kötegelt betöltése a névjegyek HBase-táblába**

A HBase több módszert tartalmaz az adatok táblába töltéséhez.  További információ: [Bulk loading](https://hbase.apache.org/book.html#arch.bulk.load) (Kötegelt betöltés).

Egy minta adatfájlt egy nyilvános blob-tárolóban található `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Az adatfájl tartalma a következő:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Igény szerint létrehozhat egy szövegfájlt, és feltöltheti a fájlt a saját tárfiókjába. Az utasításokért lásd: [Upload data for HDInsight az Apache Hadoop-feladatok](../hdinsight-upload-data.md).

Ez az eljárás használja a `Contacts` az utolsó eljárás során létrehozott HBase-tábla.

1. A nyitott ssh-kapcsolatot a következő parancsot az adatok átalakításához storefiles-fájllá alakítsa fájlt és tárolja által meghatározott relatív elérési `Dimporttsv.bulk.output`.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. A következő paranccsal töltse fel az adatokat a `/example/data/storeDataFileOutput` a HBase táblába:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Nyissa meg a HBase rendszerhéjat, és használja a `scan` paranccsal listát készíthet a tábla tartalmát.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Az Apache Hive használata az Apache HBase lekérdezéséhez

Használatával lekérdezheti a HBase táblákban lévő adatok [Apache Hive](https://hive.apache.org/). Ebben a szakaszban egy, a HBase-táblára leképezést biztosító Hive-táblát hoz létre, amellyel lekérdezheti a HBase-táblában lévő adatokat.

1. Az a nyit meg ssh-kapcsolatot, a következő paranccsal indíthatja el a Beeline:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    A Beeline-nal kapcsolatos további információkért lásd [a Hive és a Hadoop együttes, a Beeline-nal történő használatát a HDInsightban](../hadoop/apache-hadoop-use-hive-beeline.md) ismertető cikket.

1. Futtassa a következő [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) parancsfájl, a HBase táblára leképező Hive-tábla létrehozásához. Győződjön meg arról, hogy létrehozta az utasítás futtatása előtt a HBase-rendszerhéj használatával Ez a cikk korábbi részében hivatkozott.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Futtassa a következő HiveQL-parancsfájlt a HBase-tábla adatainak lekérdezéséhez:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. A kilépéshez a Beeline használata `!exit`.

1. Kilép az ssh-kapcsolatot használja `exit`.

## <a name="use-hbase-rest-apis-using-curl"></a>HBase REST API-k használata Curl használatával

A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje el a kiszolgálónak.

1. Indítsa el a környezeti változót a használat megkönnyítése érdekében. Az alábbi parancsokat szerkesztése lecserélésével `MYPASSWORD` az a fürt bejelentkezési jelszava. Cserélje le `MYCLUSTERNAME` a HBase-fürt nevére. Adja meg a parancsokat.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Használja az alábbi parancsot a meglévő HBase-táblák listázásához:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Használja az alábbi parancsot egy új, kétoszlopos családokkal rendelkező HBase-tábla létrehozásához:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    A séma a JSon formátumban van megadva.
1. Használja az alábbi parancsot néhány adat beviteléhez:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    A -d kapcsolóban megadott értékeket a base64 használatával kell kódolnia. A példában:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Személyes: név
   * Sm9obiBEb2xl: John Dole

     A [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) lehetővé teszi több (kötegelt) érték beszúrását.

1. Használja az alábbi parancsot egy sor lekéréséhez:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

További információ a HBase REST-ről: [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_rest) (Apache HBase referencia-útmutató).

> [!NOTE]  
> A HBase nem támogatja a Thriftet a HDInsightban.
>
> Amikor a Curl vagy más REST kommunikációt használ a WebHCattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája felhasználónevének és jelszavának megadásával. A fürtnevet a kérések a kiszolgálóhoz küldéséhez használt egységes erőforrás-azonosító (URI) részeként is használnia kell.
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    A következőhöz hasonló választ kell kapnia:
>   
>        {"status":"ok","version":"v1"}

## <a name="check-cluster-status"></a>A fürt állapotának ellenőrzése

A HBase a HDInsightban a fürtök megfigyelésére szolgáló webes felhasználói felülettel kapható. A webes felhasználói felülettel a régiók statisztikáit vagy információit kérheti le.

**A HBase mesterfelületének elérése**

1. Jelentkezzen be az Ambari webes felhasználói Felületet, `https://CLUSTERNAME.azurehdinsight.net` ahol `CLUSTERNAME` a HBase-fürt neve.

1. Válassza ki **HBase** a bal oldali menüből.

1. Válassza ki **Gyorshivatkozások** elemre a lap tetején mutasson az aktív Zookeeper-csomópont hivatkozására, és válassza **HBase-Mesterfelület**.  A felület egy új böngészőlapon nyílik meg:

   ![HDInsight HBase HMaster felhasználói felülete](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   A HBase-mesterfelület az alábbi részeket tartalmazza:

   - régiós kiszolgálók
   - biztonsági mentési főkiszolgálók
   - táblák
   - feladatok
   - szoftverattribútumok

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat. A HBase-parancsot is használhatja `disable 'Contacts'`. Ha nem folytatja az alkalmazás használatához, törölje a HBase-fürtöt az alábbi lépéseket követve létrehozott:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Az a **keresési** tetején típus **HDInsight**.
1. Válassza ki **HDInsight-fürtök** alatt **szolgáltatások**.
1. A HDInsight-fürtök megjelenő listában kattintson a **...**  ebben az oktatóanyagban létrehozott fürt mellett.
1. Kattintson a **Törlés** gombra. Kattintson a **Yes** (Igen) gombra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, Apache HBase-fürt létrehozása és a táblák létrehozásához és az adatok megtekintése az adott táblák a HBase rendszerhéjból. Azt is megtanulta, hogyan használhat Hive-lekérdezést a HBase-táblákban lévő adatokon, és hogyan használhatja a HBase C# REST API-kat egy HBase-tábla létrehozásához és adatok lekérdezéséhez a táblából. További tudnivalókért lásd:

> [!div class="nextstepaction"]
> [HDInsight HBase áttekintése](./apache-hbase-overview.md)