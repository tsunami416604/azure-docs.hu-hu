<properties
    pageTitle="HBase oktatóprogram: Ismerkedés a Linux-alapú HBase-fürtökkel a Hadoop rendszerben | Microsoft Azure"
    description="Ez a HBase oktatóanyag segítséget nyújt az Apache HBase Hadoop-eszközzel végzett használatának első lépéseiben a HDInsight eszközben. Táblákat hozhat létre a HBase rendszehéjból, és lekérdezheti azokat a Hive eszközzel."
    keywords="apache hbase,hbase,hbase rendszerhéj,hbase-oktatóanyag"
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/25/2016"
    ms.author="jgao"/>



# HBase oktatóanyag: Az Apache HBase Linux-alapú Hadoop-eszközzel végzett használatának első lépései a HDInsight eszközben 

[AZURE.INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Megtudhatja, hogyan hozhat létre HBase-fürtöket a HDInsight eszközben, illetve hogyan hozhat létre HBase táblákat és lekérdezéstáblákat a Hive eszközzel. Általános HBase információért lásd: [HDInsight HBase overview][hdinsight-hbase-overview] (A HDInsight HBase áttekintése).

A jelen dokumentumban lévő információk a Linux-alapú HDInsight-fürtökre jellemzőek. A Windows-alapú fürtökről információért használja az oldal tetején lévő lapválasztót az átváltáshoz.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

###Előfeltételek

Ezen HBase oktatóanyag elkezdéséhez a következőkkel kell rendelkeznie:

- **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Biztonságos rendszerhéj (SSU)](hdinsight-hadoop-linux-use-ssh-unix.md). 
- [curl](http://curl.haxx.se/download.html).

## HBase-fürt létrehozása

A következő eljárás Azure ARM sablonnal hoz létre HBase-fürtöt. Az eljárásban és egyéb fürtlétrehozási módszerekben használt paraméterek megértéséhez lásd: [Create Linux-based Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) (Linux-alapú Hadoop-fürtök létrehozása a HDInsightban).

1. Az alábbi képre kattintva megnyithat egy ARM-sablont az Azure portálon. Az ARM-sablon a nyilvános blobtárolóban található. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. A **Parameters** (Paraméterek) panelen adja meg a következőket:

    - **ClusterName** (Füret neve): Adjon nevet a létrehozandó HBase-fürtnek.
    - **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az **admin**.
    - **SSH-felhasználónév és jelszó**: Az alapértelmezett felhasználónév az **sshuser**.  Ezt át lehet nevezni.
     
    Más paraméterek opcionálisak.  
    
    Minden egyes fürt az Azure Blob Storage-fióktól függ. A fürtök törlése után az adatok megmaradnak a tárfiókban. A fürt alapértelmezett tárfiókneve a fürt neve a „store” kifejezéssel kiegészítve. A név szoftveresen kötött a sablonváltozók szakaszban.
        
3. Kattintson az **OK** gombra a paraméterek mentéséhez.
4. A **Custom deployment** (Egyéni üzembe helyezés) panelen kattintson a **Resource group** (Erőforráscsoport) legördülő listára, majd a **New** (Új) lehetőségre egy új erőforráscsoport létrehozásához.  Az erőforráscsoport egy olyan tároló, amely csoportosítja a fürtöt, a függő tárfiókot és egyéb kapcsolt erőforrásokat.
5. Kattintson a **Legal terms** (Jogi feltételek), majd a **Create** (Létrehozás) gombra.
6. Kattintson a **Létrehozás** gombra. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.


>[AZURE.NOTE] A HBase-fürtök törlése után egy másik HBase-fürtöt hozhat létre ugyanazon alapértelmezett blobtárolóval. Az új fürt felveszi az eredeti fürtben létrehozott HBase táblákat. Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat.

## Táblák létrehozása és adatok beszúrása

Az SSH-val HBase-fürtökhöz csatlakozhat, a HBase rendszerhéjjal pedig HBase táblákat hozhat létre, adatokat szúrhat be, és adatokat kérdezhet le. Az SSH Linux, Unix, OS X és Windows rendszeren való használatáról információkért lásd: [Use SSH with Linux-based Hadoop on HDInsight from Linux, Unix, or OS X](hdinsight-hadoop-linux-use-ssh-unix.md) (Az SSH használata a HDInsight-ra épülő Linux-alapú Hadooppal Linux, Unix vagy OS X rendszeren) és [Use SSH with Linux-based Hadoop on HDInsight from Windows](hdinsight-hadoop-linux-use-ssh-windows.md) (Az SSH használata a HDInsightra épülő Linux-alapú Hadooppal Windows rendszeren).
 

A legtöbbek számára az adatok táblázatos formátumban jelennek meg:

![hdinsight hbase táblázatos adatok][img-hbase-sample-data-tabular]

A HBase eszközben, amely a BigTable implementációja, ugyanezen az adatok a következőképpen néznek ki:

![hdinsight hbase bigtable adatok][img-hbase-sample-data-bigtable]

Több értelme lesz a következő eljárás befejezése után.  


**A HBase rendszerhéj használata**

1. Az SSH-ból futtassa az alábbi parancsot:

        hbase shell

4. HBase létrehozása két oszlopcsaláddal:

        create 'Contacts', 'Personal', 'Office'
        list
5. Adatok beszúrása:

        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'

    ![hdinsight hadoop hbase rendszerhéj][img-hbase-shell]

6. Egyetlen sor lekérése

        get 'Contacts', '1000'

    Ugyanazokat az eredményeket látja, mint a vizsgálat parancskor, mert csak egy sor van.

    A HBase táblasémáról további információért lásd: [Introduction to HBase Schema Design][hbase-schema] (Bevezetés a HBase sématervezésbe). További HBase parancsokért lásd: [Apache HBase reference guide][hbase-quick-start] (Apache HBase referencia-útmutató).

6. Kilépés a rendszerhéjból

        exit



**Adatok kötegelt betöltése a névjegyek HBase táblájába**

A HBase több módszert tartalmaz az adatok táblába töltéséhez.  További információ: [Bulk loading](http://hbase.apache.org/book.html#arch.bulk.load) (Kötegelt betöltés).


Egy mintául szolgáló adatfájl lett feltöltve egy nyilvános blobtárolóba, *wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt*.  Az adatfájl tartalma a következő:

    8396    Calvin Raji     230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller   397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee       870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes      599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander 670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander   998-555-0171    230-555-0200    771 Northridge Drive

Létrehozhat egy szövegfájlt, és feltöltheti a fájlt a saját tárfiókjába, ha szeretné. Az utasításokért lásd: [Upload data for Hadoop jobs in HDInsight][hdinsight-upload-data] (Adatok feltöltése Hadoop-feladatokhoz a HDInsightban).

> [AZURE.NOTE] Ez az eljárás az utolsó eljárás során létrehozott Contacts HBase táblát használja.

1. Az SSH-ból futtassa az alábbi parancsot, hogy az adatfájlt StoreFiles-fájllá alakítsa, és a Dimporttsv.bulk.output által meghatározott relatív elérési úton tárolja:  Ha a HBase rendszerhéjban van, a kilépés paranccsal lépjen ki.

        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

4. Futtassa a következő parancsot, hogy adatokat töltsön fel az /example/data/storeDataFileOutput mappából a HBase táblába:

        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts

5. Megnyithatja a HBase rendszerhéjat, és a vizsgálat paranccsal listázhatja a tábla tartalmát.



## A Hive használata a HBase lekérdezéséhez

A HBase táblákban lévő adatokat a Hive eszközzel kérdezheti le. Ez a szakasz olyan Hive táblát hoz létre, amely a HBase táblára képez le, és azzal kérdezi le a HBase táblában lévő adatokat.

1. Nyissa meg a **PuTTY** eszközt, és csatlakozzon a fürthöz.  Lásd az előző eljárás utasításait.
2. Nyissa meg a Hive rendszerhéjat.

       hive
3. Futtassa a következő HiveQL-parancsfájlt, hogy egy, a HBase táblára leképező Hive táblát hozzon létre. Ellenőrizze, hogy létrehozta-e az oktatóanyag korábbi részében hivatkozott mintatáblát az utasítás futtatása előtt a HBase rendszerhéjjal.

        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');

2. Futtassa a következő HiveQL-parancsfájlt. A Hive-lekérdezés lekérdezi a HBase táblában lévő adatokat:

        SELECT count(*) FROM hbasecontacts;

## HBase REST API-k használata Curl használatával

> [AZURE.NOTE] Amikor a Curl vagy más REST kommunikációt használ a WebHCattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája felhasználónevének és jelszavának megadásával. A fürtnevet a kérések kiszolgálóhoz küldéséhez használt egységes erőforrás-azonosító (URI) részeként is használnia kell.
>
> Ezen szakasz parancsaiban cserélje le a **USERNAME** elemet a fürthöz hitelesíteni kívánt felhasználóval, és a **PASSWORD** elemet pedig a felhasználói fiók jelszavával. Cserélje le a **CLUSTERNAME** elemet a fürt nevére.
>
> A REST API védelméről [alapszintű hitelesítés](http://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje a kiszolgálóhoz.

1. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status

    A következőhöz hasonló választ kell kapnia:

        {"status":"ok","version":"v1"}

    Ezen parancs paraméterei a következők:

    * **-u** - A kérés hitelesítéséhez használt felhasználónév és jelszó.
    * **-G** - Jelzi, hogy ez egy GET kérés.

2. Használja az alábbi parancsot a meglévő HBase táblák listázásához:

        curl -u <UserName>:<Password> \
        -G https://<ClusterName>.azurehdinsight.net/hbaserest/

3. Használja az alábbi parancsot egy új HBase tábla létrehozásához két oszlopcsaláddal:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/schema" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
        -v

    A séma a JSon formátumban van megadva.

4. Használja az alábbi parancsot néhány adat beviteléhez:

        curl -u <UserName>:<Password> \
        -X PUT "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
        -H "Accept: application/json" \
        -H "Content-Type: application/json" \
        -d "{\"Row\":{\"key\":\"MTAwMA==\",\"Cell\":{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}}}" \
        -v

    A -d kapcsolóban megadott értékeket a base64 használatával kell kódolnia.  A példában:

    - MTAwMA==: 1000
    - UGVyc29uYWw6TmFtZQ==: Personal:Name
    - Sm9obiBEb2xl: John Dole

    A [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) lehetővé teszi több (kötegelt) érték beszúrását.

5. Használja az alábbi parancsot egy sor lekéréséhez:

        curl -u <UserName>:<Password> \
        -X GET "https://<ClusterName>.azurehdinsight.net/hbaserest/Contacts1/1000" \
        -H "Accept: application/json" \
        -v

További információ a HBase REST-ről: [Apache HBase Reference Guide](https://hbase.apache.org/book.html#_rest) (Apache HBase referencia-útmutató).

## A fürt állapotának ellenőrzése

A HBase a HDInsightban a fürtök megfigyelésére szolgáló webes felhasználói felülettel kapható. A webes felhasználói felülettel a régiók statisztikáit vagy információit kérheti le.

Az SSH-val helyi kérések, például webes kérések bújtatását is elvégezheti a HDInsight-fürthöz. A rendszer ilyenkor úgy irányítja a kérést a kért erőforráshoz, mintha a HDInsight-fürt átjárócsomópontból származna. További információkért lásd: [Use SSH with Linux-based Hadoop on HDInsight from Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel) (Az SSH használata a HDInsightra épülő Linux-alapú Hadooppal Windows rendszerben)

**SSH-alagútkezelési munkamenet létesítése**

1. Nyissa meg a **PuTTY** eszközt.  
2. Ha a felhasználói fiók létrehozásakor egy SSH-kulcsot adott meg a létrehozásai folyamat során, a következő lépéseket kell elvégeznie a fürt hitelesítéséhez használt titkos kulcs kiválasztásához:

    A **Category** (Kategória) szakaszban bontsa ki a **Connection** (Kapcsolat), majd az **SSH** elemet, és válassza az **Auth** (Hitelesítés) lehetőséget. Végül kattintson a **Browse** (Tallózás) gombra, és válassza ki a titkos kulcsot tartalmazó .ppk fájlt.

3. A **Category** (Kategória) szakaszban kattintson a **Session** (Munkamenet) elemre.
4. A PuTTY munkamenet képernyőjén lévő Basic (Alapszintű) beállításokban adja meg a következő értékeket:

    - **Host Name** (Állomásnév): a HDInsight-kiszolgáló SSH-címe a Host Name (Állomásnév) (vagy IP-cím) mezőben. Az SSH-cím a fürtnév, majd **-ssh.azurehdinsight.net**. Például: *mycluster-ssh.azurehdinsight.net*.
    - **Port**: 22. A 0 átjárócsomóponton az ssh port a 22.  
5. A párbeszédpanel bal oldalán lévő **Category** (Kategória) szakaszban bontsa ki a **Connection** (Kapcsolat), majd az **SSH** elemet, majd kattintson a **Tunnels** (Alagutak) lehetőségre.
6. Adja meg a következő információkat az SSH-porttovábbító űrlapot vezérlő beállításokban:

    - **Source port** (Forrásport) - Az ügyfélen az a port, amelyet továbbítani szeretne. Például: 9876.
    - **Dynamic** (Dinamikus) - Lehetővé teszi a dinamikus SOCKS proxy útválasztást.
7. Kattintson az **Add** (Hozzáadás) gombra a beállítások hozzáadásához.
8. Kattintson a párbeszédpanel alján lévő **Open** (Megnyitás) gombra egy SSH-kapcsolat megnyitásához.
9. Amikor a rendszer kéri, jelentkezzen be a kiszolgálóra egy SSH-fiókkal. Ez SSH-munkamenetet létesít, és engedélyezi az alagutat.

**A zookeeperek FQDN-jének megkeresése az Ambarival**

1. Keresse fel a https://<ClusterName>.azurehdinsight.net/ webhelyet.
2. Írja be kétszer a fürt felhasználói fiókjának hitelesítő adatait.
3. A bal oldali menüben kattintson a **zookeeper** elemre.
4. Kattintson a Summary (Összefoglalás) listában a három **ZooKeeper Server** (ZooKeeper-kiszolgáló) hivatkozás egyikére.
5. Másolja a **Hostname** (Állomásnév) értékét. Például zk0-CLUSTERNAME.xxxxxxxxxxxxxxxxxxxx.cx.internal.cloudapp.net.

**Ügyfélprogram (Firefox) konfigurálása és a fürtállapot ellenőrzése**

1. Nyissa meg a Firefox programot.
2. Kattintson a **Menü megnyitása** gombra.
3. Kattintson a **Beállítások** gombra.
4. Kattintson a **Speciális** elemre, kattintson a **Hálózat** elemre, majd kattintson a **Beállítások** parancsra.
5. Válassza a **Kézi proxybeállítás** lehetőséget.
6. Írja be a következő értékeket:

    - **Socks gép**: localhost
    - **Port**: Használja azt a portot, amelyet a Putty SSH-bújtatásban konfigurált.  Például: 9876.
    - **SOCKS v5**: (kiválasztva)
    - **Távoli DNS**: (kiválasztva)
7. A módosítások mentéséhez kattintson az **OK** gombra.
8. Nyissa meg a következő címet: http://&lt;ZooKeeper teljes tartományneve>:60010/master-status.

Magas rendelkezésre állású fürtökön megtalálja a webes felhasználói felületet szolgáltató, aktuálisan aktív HBase főcsomópont hivatkozását.

##A fürt törlése

Az inkonzisztenciák elkerülése érdekében javasoljuk, hogy a fürt törlése előtt tiltsa le a HBase-táblákat.

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Következő lépések

A HDInsight ezen HBase oktatóprogramjában megtanulta, hogyan hozhat létre HBase-fürtöt, hogyan hozhat létre táblákat, és tekintheti meg ezen táblák adatait a HBase rendszerhéjból. Azt is megtanulta, hogyan használhat Hive-lekérdezést a HBase táblákban lévő adatokon, és hogyan használhatja a HBase C# REST API-kat egy HBase tábla létrehozásához és adatok lekérdezéséhez a táblából.

További tudnivalókért lásd:

- [HDInsight HBase overview][hdinsight-hbase-overview] (A HDInsight HBase áttekintése): A HBase egy Apache, nyílt forráskódú, a Hadoopra épülő NoSQL-adatbázis, amely véletlenszerű hozzáférést és erős konzisztenciát biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára.


[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png



<!--HONumber=sep16_HO1-->


