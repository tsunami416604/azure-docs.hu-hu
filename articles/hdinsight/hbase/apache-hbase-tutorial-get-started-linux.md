---
title: Oktatóanyag – Az Apache HBase használata az Azure HDInsightban
description: Kövesse ezt az Apache HBase oktatóanyagot a hadoop HDInsight-on való használatának megkezdéséhez. Táblákat hozhat létre a HBase rendszehéjból, és lekérdezheti azokat a Hive eszközzel.
keywords: hbasecommand,hbase példa
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: hrasheed
ms.openlocfilehash: e43d2d64535085a9b22d2febc761fc7026498ba8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71077140"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Oktatóanyag: Az Apache HBase használata az Azure HDInsightban

Ez az oktatóanyag bemutatja, hogyan hozhat létre Apache HBase-fürtöt az Azure HDInsightban, hogyan hozhat létre HBase-táblákat és lekérdezési táblákat az Apache Hive használatával.  A HBase-re vonatkozó általános információért lásd: [HDInsight HBase overview](./apache-hbase-overview.md) (A HDInsight HBase áttekintése).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Apache HBase fürt létrehozása
> * HBase táblák létrehozása és adatok beszúrása
> * Apache Hive használata az Apache HBase lekérdezéséhez
> * HBase REST API-k használata Curl használatával
> * A fürt állapotának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

* Bash. A példák ebben a cikkben használja a Bash shell a Windows 10 a curl parancsokat. A windows [10-es Windows-alrendszer telepítési útmutatójában](https://docs.microsoft.com/windows/wsl/install-win10) a windows 10-es operációs rendszer telepítési útmutatójában talál.  Más [Unix héjak](https://www.gnu.org/software/bash/) is működni fog.  A curl példák, néhány kisebb módosításokkal, működhet a Windows parancssorban.  Másik lehetőségként használhatja a Windows PowerShell parancsmag [meghívás-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Apache HBase fürt létrehozása

Az alábbi eljárás egy Azure Resource Manager-sablont használ egy HBase-fürt és a függő alapértelmezett Azure Storage-fiók létrehozásához. Az eljárásban és egyéb fürtlétrehozási módszerekben használt paraméterek megértéséhez lásd: [Create Linux-based Hadoop clusters in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban).

1. Válassza ki az alábbi képet a sablon megnyitásához az Azure Portalon. A sablon az [Azure rövid útmutató sablonjaiban](https://azure.microsoft.com/resources/templates/)található.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Az **Egyéni üzembe helyezés** panelen adja meg a következő értékeket:

    |Tulajdonság |Leírás |
    |---|---|
    |Előfizetés|Válassza ki a fürt létrehozásához használt Azure-előfizetést.|
    |Erőforráscsoport|Hozzon létre egy Azure Erőforrás-kezelési csoportot, vagy használjon egy meglévőt.|
    |Hely|Adja meg az erőforráscsoport helyét. |
    |ClusterName|Adja meg a HBase-fürt nevét.|
    |A fürt bejelentkezési neve és jelszava|Az alapértelmezett bejelentkezési név az **admin**.|
    |SSH-felhasználónév és jelszó|Az alapértelmezett felhasználónév az **sshuser**.|

    Más paraméterek opcionálisak.  

    Minden egyes fürt az Azure Storage-fióktól függ. A fürtök törlése után az adatok megmaradnak a tárfiókban. A fürt alapértelmezett tárfiókneve a fürt neve a „store” kifejezéssel kiegészítve. A név szoftveresen kötött a sablonváltozók szakaszban.

3. Válassza **az Elfogadom a fent meghatározott feltételeket,** majd a **Vásárlás**lehetőséget. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

A HBase-fürtök törlése után egy másik HBase-fürtöt hozhat létre ugyanazon alapértelmezett blobtárolóval. Az új fürt felveszi az eredeti fürtben létrehozott HBase-táblákat. Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat.

## <a name="create-tables-and-insert-data"></a>Táblák létrehozása és adatok beszúrása

Az SSH segítségével csatlakozhat a HBase-fürtökhöz, majd [az Apache HBase Shell](https://hbase.apache.org/0.94/book/shell.html) segítségével HBase táblákat hozhat létre, adatokat szúrhat be és lekérdezési adatokat.

A legtöbbek számára az adatok táblázatos formátumban jelennek meg:

![HDInsight Apache HBase táblázatos adatok](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

A HBase (végrehajtása [Cloud BigTable](https://cloud.google.com/bigtable/)), ugyanazokaz adatok néz ki:

![HDInsight Apache HBase BigTable adatok](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**A Hbase-rendszerhéj használata**

1. A `ssh` Parancs segítségével csatlakozzon a HBase fürthöz. Az alábbi parancs szerkesztése a fürt nevének cseréjével, `CLUSTERNAME` majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. A `hbase shell` HBase interaktív rendszerhéj indításához használja a parancsot. Írja be a következő parancsot az SSH-kapcsolatba:

    ```bash
    hbase shell
    ```

1. A `create` parancs segítségével hozzon létre egy HBase táblát kétoszlopos családokkal. A tábla- és oszlopnevekben a kis- és nagybetűket nem lehet figyelembe. Írja be a következő parancsot:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. A `list` parancs segítségével a HBase összes tábláját listázhassa. Írja be a következő parancsot:

    ```hbase
    list
    ```

1. A `put` parancs segítségével értékeket szúrhat be egy adott tábla megadott sorának megadott oszlopába. Írja be a következő parancsokat:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. A `scan` parancs segítségével beszkandelheti és visszaadhatja a `Contacts` táblaadatait. Írja be a következő parancsot:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Apache Hadoop HBase rendszerhéj](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. A `get` parancs segítségével lekéri egy sor tartalmát. Írja be a következő parancsot:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Hasonló eredményeket lát, `scan` mint a parancs használata, mert csak egy sor van.

    A HBase táblasémáról további információt az [Apache HBase sématervezés – bevezetés című témakörben talál.](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf) További Hbase-parancsokért lásd: [Apache HBase reference guide](https://hbase.apache.org/book.html#quickstart) (Apache HBase referencia-útmutató).

1. A `exit` Parancs segítségével állítsa le a HBase interaktív rendszerhéjat. Írja be a következő parancsot:

    ```hbaseshell
    exit
    ```

**Adatok kötegelt betöltése a névjegyek HBase-táblába**

A HBase több módszert tartalmaz az adatok táblába töltéséhez.  További információ: [Bulk loading](https://hbase.apache.org/book.html#arch.bulk.load) (Kötegelt betöltés).

Egy minta adatfájl található a következő nyilvános blobtárolóban található: `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Az adatfájl tartalma a következő:

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

Igény szerint létrehozhat egy szövegfájlt, és feltöltheti a fájlt a saját tárfiókjába. Az utasításokat az [Apache Hadoop-feladatok adatainak feltöltése a HDInsightban című témakörben találja.](../hdinsight-upload-data.md)

Ez az `Contacts` eljárás az utolsó eljárásban létrehozott HBase táblát használja.

1. A nyitott ssh kapcsolaton futtassa a következő parancsot az adatfájl StoreFiles `Dimporttsv.bulk.output`fájllá alakításához, és tárolja a.

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Futtassa a következő parancsot az adatok feltöltéséhez a HBase táblába: `/example/data/storeDataFileOutput`

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Megnyithatja a HBase rendszerhéjat, és a `scan` paranccsal felsorolhatja a táblázat tartalmát.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Apache Hive használata az Apache HBase lekérdezéséhez

A HBase-táblákban az [Apache Hive](https://hive.apache.org/)használatával lekérdezheti az adatokat. Ebben a szakaszban egy, a HBase-táblára leképezést biztosító Hive-táblát hoz létre, amellyel lekérdezheti a HBase-táblában lévő adatokat.

1. A nyitott ssh kapcsolatból a következő paranccsal indítsa el a Beeline-t:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    A Beeline-nal kapcsolatos további információkért lásd [a Hive és a Hadoop együttes, a Beeline-nal történő használatát a HDInsightban](../hadoop/apache-hadoop-use-hive-beeline.md) ismertető cikket.

1. Futtassa a következő [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) parancsfájlt egy Hive-tábla létrehozásához, amely leképezi a HBase-táblát. Győződjön meg arról, hogy a jelen cikkben korábban hivatkozott mintatáblát a HBase rendszerhéj használatával hozta létre a jelen utasítás futtatása előtt.

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

1. A Beeline-ból `!exit`való kilépéshez használja a használatát.

1. Az ssh kapcsolatból `exit`való kilépéshez használja a használatát.

## <a name="use-hbase-rest-apis-using-curl"></a>HBase REST API-k használata Curl használatával

A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje el a kiszolgálónak.

1. Környezeti változó kezdeményezése a könnyű használat érdekében. Az alábbi parancsokat `MYPASSWORD` a fürt bejelentkezési jelszavával szerkeszthetjük. Cserélje `MYCLUSTERNAME` le a HBase-fürt nevére. Ezután adja meg a parancsokat.

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
   * UGVyc29uYWw6TmFtZQ==: Personal:Name
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

1. Jelentkezzen be az Ambari `https://CLUSTERNAME.azurehdinsight.net` web `CLUSTERNAME` felhasználói felületére, ahol a HBase-fürt neve található.

1. Válassza a Bal oldali menü **HBase** parancsát.

1. Válassza a **gyorshivatkozások** lehetőséget a lap tetején, mutasson az aktív Zookeeper csomópontra, és válassza a **HBase főfelhasználói felületét.**  A felület egy új böngészőlapon nyílik meg:

   ![HDInsight Apache HBase HMaster felhasználói felület](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   A HBase-mesterfelület az alábbi részeket tartalmazza:

   - régiós kiszolgálók
   - biztonsági mentési főkiszolgálók
   - táblák
   - feladatok
   - szoftverattribútumok

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat. Használhatja a HBase `disable 'Contacts'`parancsot . Ha nem fogja tovább használni ezt az alkalmazást, törölje a következő lépésekkel létrehozott HBase-fürtöt:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. A felső **keresőmezőbe** írja be a **HDInsight kifejezést.**
1. Válassza a **HDInsight-fürtök et a** **Szolgáltatások csoportban.**
1. A megjelenő HDInsight-fürtök listájában kattintson az oktatóanyaghoz létrehozott fürt melletti **...** elemre.
1. Kattintson a **Törlés** gombra. Kattintson **az Igen gombra.**

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre apache HBase-fürtöt, és hogyan hozhat létre táblákat, és hogyan tekintheti meg az adatokat ezekben a táblákban a HBase rendszerhéjból. Azt is megtanulta, hogyan használhat Hive-lekérdezést a HBase-táblákban lévő adatokon, és hogyan használhatja a HBase C# REST API-kat egy HBase-tábla létrehozásához és adatok lekérdezéséhez a táblából. További tudnivalókért lásd:

> [!div class="nextstepaction"]
> [A HDInsight HBase áttekintése](./apache-hbase-overview.md)