---
title: Java HBase ügyfél - Azure HDInsight |} Microsoft Docs
description: Útmutató Apache Maven segítségével összeállíthat egy Apache HBase Java-alapú alkalmazást, majd központilag telepítenie kell az Azure hdinsight HBase.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: ''
ms.assetid: 1d1ed180-e0f4-4d1c-b5ea-72e0eda643bc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: larryfr
ms.openlocfilehash: 564bfb9c80fe835bd5defb4321607e0970c9f0aa
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
# <a name="build-java-applications-for-apache-hbase"></a>Az Apache HBase Java-alkalmazások létrehozása

Megtudhatja, hogyan hozzon létre egy [Apache HBase](http://hbase.apache.org/) alkalmazás Java nyelven. Az Azure HDInsight HBase majd használni az alkalmazást.

A lépéseket, ez a dokumentum használatát [Maven](http://maven.apache.org/) létrehozásához, és a projekt felépítéséhez. Maven egy a projekt szoftverkezelés és szövegértést eszköz, amely lehetővé teszi a szoftverek, dokumentáció és a Java-projektek jelentések létrehozásához.

> [!NOTE]
> A jelen dokumentumban leírt lépések alapján legutóbb történő teszteléskor az HDInsight 3.6.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy HDInsight-fürt által használt Linux igényelnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Követelmények

* [Java-platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 8 vagy újabb.

    > [!NOTE]
    > HDInsight 3.5-ös és nagyobb Java 8 igényel. HDInsight a korábbi Java 7 igényelnek.

* [Maven 3](http://maven.apache.org/)

* [A HBase Linux-alapú Azure HDInsight fürt](apache-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

## <a name="create-the-project"></a>A projekt létrehozása

1. A parancssorban a fejlesztési környezetet, módosítsa a könyvtárat a helyének például a projekt létrehozásához `cd code\hbase`.

2. Használja a **mvn** parancsot, amely Maven együtt települ, a állványok a projekt létrehozásához.

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [!NOTE]
    > Ha a PowerShell használata esetén kell tenni a `-D` az idézőjelek közé foglalt paraméterek.
    >
    > `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=hbaseapp" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Ez a parancs létrehoz egy könyvtárat a neve megegyezik a **artifactid szakaszát** paraméter (**hbaseapp** ebben a példában.) Ez a könyvtár a következő elemeket tartalmazza:

   * **pom.xml**: A projekt Object Model ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) és a projekt felépítéséhez használt információkat és konfigurációs részleteit tartalmazza.
   * **src**: tartalmazó könyvtárat a **main/java/com/microsoft/példák** könyvtárban, ahol az alkalmazás szerzőként.

3. Törölje a `src/test/java/com/microsoft/examples/apptest.java` fájlt. Nem használható ebben a példában.

## <a name="update-the-project-object-model"></a>Frissítés a projekt Hálózatiobjektum-modellje

1. Szerkessze a `pom.xml` fájlt, és adja hozzá az alábbi kódot a `<dependencies>` szakasz:

   ```xml
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>1.1.2</version>
    </dependency>
    <dependency>
        <groupId>org.apache.phoenix</groupId>
        <artifactId>phoenix-core</artifactId>
        <version>4.4.0-HBase-1.1</version>
    </dependency>
   ```

    Ez a szakasz azt jelzi, hogy kell-e a projekt **hbase-ügyfél** és **phoenix-core** összetevőket. Fordítási időben a függőségek az alapértelmezett Maven tárházból lesznek letöltve. Használhatja a [Maven központi tárház keresési](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) további információt a függőség.

   > [!IMPORTANT]
   > A hbase-ügyfél verziószáma meg kell egyeznie a HBase a HDInsight-fürthöz biztosított verzióját. Használja az alábbi táblázatban található a megfelelő verziószámot.

   | HDInsight-fürt verziószáma | A HBase-verziót kell használni |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3-as, 3.4, 3.5-ös és 3.6. |1.1.2 |

    A HDInsight-verziókról és összetevők további információkért lásd: [Mik a különböző érhető el a HDInsight Hadoop-összetevők](../hdinsight-component-versioning.md).

3. Adja hozzá a következő kódot a **pom.xml** fájlt. Ez a szöveg belül kell lennie a `<project>...</project>` címkék a fájlban, például közötti `</dependencies>` és `</project>`.

   ```xml
    <build>
        <sourceDirectory>src</sourceDirectory>
        <resources>
        <resource>
            <directory>${basedir}/conf</directory>
            <filtering>false</filtering>
            <includes>
            <include>hbase-site.xml</include>
            </includes>
        </resource>
        </resources>
        <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
            </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>2.3</version>
            <configuration>
            <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                </transformer>
            </transformers>
            </configuration>
            <executions>
            <execution>
                <phase>package</phase>
                <goals>
                <goal>shade</goal>
                </goals>
            </execution>
            </executions>
        </plugin>
        </plugins>
    </build>
   ```

    Ez a szakasz konfigurál egy erőforrást (`conf/hbase-site.xml`), amely a HBase konfigurációs információkat tartalmaz.

   > [!NOTE]
   > Megadhatja a konfigurációs értékeket keresztül kódot is. A Megjegyzések a `CreateTable` példa.

    Ez a szakasz is konfigurálja a [Maven fordító beépülő modul](http://maven.apache.org/plugins/maven-compiler-plugin/) és [Maven árnyalatát beépülő modul](http://maven.apache.org/plugins/maven-shade-plugin/). A fordítóprogram beépülő modul segítségével lefordítani a topológia. A beépülő modul színárnyalat annak megelőzésére szolgál a JAR-csomagot, amely épül fel Maven licenc rejlő ismétlődést. A beépülő modul segítségével egy "ismétlődő licencfájlok" hiba megakadályozza a HDInsight-fürt futási időben. Maven-árnyalatát-beépülő modul a használatával a `ApacheLicenseResourceTransformer` megvalósítási megakadályozza, hogy a hibát.

    A maven-árnyalatát-beépülő modul is létrehoz egy uber jar, amely tartalmazza az alkalmazás által igényelt összes függősége.

4. Mentse a `pom.xml` fájlt.

5. Hozzon létre egy könyvtárat nevű `conf` a a `hbaseapp` könyvtár. Ez a könyvtár konfigurációs adatokat a HBase való kapcsolódáshoz használatos.

6. A HBase konfigurációs másolása a HBase-fürtöt, hogy a következő parancs segítségével a `conf` könyvtár. Cserélje le `USERNAME` az SSH-bejelentkezéskor nevével. Cserélje le `CLUSTERNAME` a HDInsight fürt nevű:

    ```bash
    scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
    ```

   További információk az `ssh` és `scp`, lásd: [az SSH a Hdinsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-application"></a>Az alkalmazás létrehozása

1. Lépjen a `hbaseapp/src/main/java/com/microsoft/examples` könyvtár, és nevezze át a app.java fájlt `CreateTable.java`.

2. Nyissa meg a `CreateTable.java` fájlt, és cserélje le a meglévő tartalmat, a következő szövegű üzenettel:

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HBaseAdmin;
    import org.apache.hadoop.hbase.HTableDescriptor;
    import org.apache.hadoop.hbase.TableName;
    import org.apache.hadoop.hbase.HColumnDescriptor;
    import org.apache.hadoop.hbase.client.HTable;
    import org.apache.hadoop.hbase.client.Put;
    import org.apache.hadoop.hbase.util.Bytes;

    public class CreateTable {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Example of setting zookeeper values for HDInsight
        // in code instead of an hbase-site.xml file
        //
        // config.set("hbase.zookeeper.quorum",
        //            "zookeepernode0,zookeepernode1,zookeepernode2");
        //config.set("hbase.zookeeper.property.clientPort", "2181");
        //config.set("hbase.cluster.distributed", "true");
        //
        //NOTE: Actual zookeeper host names can be found using Ambari:
        //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

        //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
        config.set("zookeeper.znode.parent","/hbase-unsecure");

        // create an admin object using the config
        HBaseAdmin admin = new HBaseAdmin(config);

        // create the table...
        HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
        // ... with two column families
        tableDescriptor.addFamily(new HColumnDescriptor("name"));
        tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
        admin.createTable(tableDescriptor);

        // define some people
        String[][] people = {
            { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
            { "2", "Franklin", "Holtz", "franklin@contoso.com" },
            { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
            { "4", "Rae", "Schroeder", "rae@contoso.com" },
            { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
            { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

        HTable table = new HTable(config, "people");

        // Add each person to the table
        //   Use the `name` column family for the name
        //   Use the `contactinfo` column family for the email
        for (int i = 0; i< people.length; i++) {
            Put person = new Put(Bytes.toBytes(people[i][0]));
            person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
            person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
            person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
            table.put(person);
        }
        // flush commits and close the table
        table.flushCommits();
        table.close();
        }
    }
   ```

    Ez a kód a **CreateTable** osztályt, amely egy nevű táblát hoz létre **személyek** , és feltöltheti olyan előre definiált felhasználók.

3. Mentse a `CreateTable.java` fájlt.

4. Az a `hbaseapp/src/main/java/com/microsoft/examples` könyvtár, hozzon létre egy fájlt `SearchByEmail.java`. Ez a fájl tartalmát a következő szöveg használata:

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HTable;
    import org.apache.hadoop.hbase.client.Scan;
    import org.apache.hadoop.hbase.client.ResultScanner;
    import org.apache.hadoop.hbase.client.Result;
    import org.apache.hadoop.hbase.filter.RegexStringComparator;
    import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
    import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
    import org.apache.hadoop.hbase.util.Bytes;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class SearchByEmail {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Use GenericOptionsParser to get only the parameters to the class
        // and not all the parameters passed (when using WebHCat for example)
        String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
        if (otherArgs.length != 1) {
            System.out.println("usage: [regular expression]");
            System.exit(-1);
        }

        // Open the table
        HTable table = new HTable(config, "people");

        // Define the family and qualifiers to be used
        byte[] contactFamily = Bytes.toBytes("contactinfo");
        byte[] emailQualifier = Bytes.toBytes("email");
        byte[] nameFamily = Bytes.toBytes("name");
        byte[] firstNameQualifier = Bytes.toBytes("first");
        byte[] lastNameQualifier = Bytes.toBytes("last");

        // Create a regex filter
        RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
        // Attach the regex filter to a filter
        //   for the email column
        SingleColumnValueFilter filter = new SingleColumnValueFilter(
            contactFamily,
            emailQualifier,
            CompareOp.EQUAL,
            emailFilter
        );

        // Create a scan and set the filter
        Scan scan = new Scan();
        scan.setFilter(filter);

        // Get the results
        ResultScanner results = table.getScanner(scan);
        // Iterate over results and print  values
        for (Result result : results ) {
            String id = new String(result.getRow());
            byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
            String firstName = new String(firstNameObj);
            byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
            String lastName = new String(lastNameObj);
            System.out.println(firstName + " " + lastName + " - ID: " + id);
            byte[] emailObj = result.getValue(contactFamily, emailQualifier);
            String email = new String(emailObj);
            System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
        }
        results.close();
        table.close();
        }
    }
   ```

    A **SearchByEmail** osztály használható lekérdezze a sorok e-mail cím alapján. Reguláris kifejezés szűrőt használja, mert biztosíthat egy karakterlánc vagy egy reguláris kifejezést az osztály használata esetén.

5. Mentse a `SearchByEmail.java` fájlt.

6. Az a `hbaseapp/src/main/hava/com/microsoft/examples` könyvtár, hozzon létre egy fájlt `DeleteTable.java`. Ez a fájl tartalmát a következő szöveg használata:

   ```java
    package com.microsoft.examples;
    import java.io.IOException;

    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.hbase.HBaseConfiguration;
    import org.apache.hadoop.hbase.client.HBaseAdmin;

    public class DeleteTable {
        public static void main(String[] args) throws IOException {
        Configuration config = HBaseConfiguration.create();

        // Create an admin object using the config
        HBaseAdmin admin = new HBaseAdmin(config);

        // Disable, and then delete the table
        admin.disableTable("people");
        admin.deleteTable("people");
        }
    }
   ```

    Ez az osztály a szükségtelenné vált letiltásával és a táblázat által létrehozott eldobása ebben a példában létrehozott HBase táblákat a `CreateTable` osztály.

7. Mentse a `DeleteTable.java` fájlt.

## <a name="build-and-package-the-application"></a>Hozza létre, és az alkalmazás becsomagolása

1. Az a `hbaseapp` directory, az alkalmazást tartalmazó JAR-fájl létrehozásához a következő paranccsal:

    ```bash
    mvn clean package
    ```

    Ez a parancs hoz létre, és csomagolja be a .jar fájlt az alkalmazás.

2. A parancs befejeződésekor a `hbaseapp/target` directory nevű fájlt tartalmaz `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]
   > A `hbaseapp-1.0-SNAPSHOT.jar` fájl egy uber jar. Az alkalmazás futtatásához szükséges összes függősége tartalmaz.


## <a name="upload-the-jar-and-run-jobs-ssh"></a>Töltse fel a JAR-feladatok és futtatása (SSH)

Az alábbi lépéseket használata `scp` a JAR átmásolása a HBase a HDInsight-fürt elsődleges átjárócsomópontjához. A `ssh` parancs majd segítségével csatlakozzon a fürthöz, és futtassa a példa közvetlenül a központi csomópontján.

1. Töltse fel a jar a fürthöz, a következő paranccsal:

    ```bash
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

    Cserélje le `USERNAME` az SSH-bejelentkezéskor nevével. Cserélje le `CLUSTERNAME` elemet a HDInsight fürt nevére.

2. Ha csatlakozni szeretne a HBase-fürtöt, a következő paranccsal:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Cserélje le `USERNAME` az SSH-bejelentkezéskor nevét. Cserélje le `CLUSTERNAME` elemet a HDInsight fürt nevére.

3. A Java-alkalmazás egy HBase tábla létrehozásához használja a következő parancsot:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Ezzel a paranccsal létrejön egy HBase tábla nevű **személyek**, és tölti fel az adatokat.

4. Keresse meg a táblában tárolt e-mail címeket, használja a következő parancsot:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    A következő eredmény jelenik meg:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Törölni kívánja a táblázatot, használja a következő parancsot:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Töltse fel a JAR-feladatok és futtatása (PowerShell)

Az alábbi lépéseket az Azure PowerShell használatával a JAR feltölteni az alapértelmezett tároló a HBase-fürtöt. HDInsight-parancsmagokat szolgálnak majd a példák távolról futtatni.

1. Miután telepítése és konfigurálása az Azure PowerShell, hozzon létre egy fájlt `hbase-runner.psm1`. Ez a fájl tartalmát a következő szöveg használata:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzureRmHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzureStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            throw "No active Azure subscription found! If you have a subscription, use the Connect-AzureRmAccount cmdlet to login to your subscription."
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Ez a fájl két lehetővé tevő modulokat tartalmaz:

   * **Adja hozzá HDInsightFile** - használt fájlok feltöltése a fürt
   * **Start-HBaseExample** - a korábban létrehozott osztályok futtatásához használt

2. Mentse a `hbase-runner.psm1` fájlt.

3. Nyisson meg egy új Azure PowerShell-ablakot, lépjen a `hbaseapp` könyvtár, és futtassa a következő parancsot:

    ```powershell
    PS C:\ Import-Module c:\path\to\hbase-runner.psm1
    ```

    Az elérési utat módosítsa a helyét a `hbase-runner.psm1` korábban létrehozott fájlt. Ez a parancs a modul az Azure PowerShell regisztrálja.

4. Az alábbi parancs segítségével töltse fel a `hbaseapp-1.0-SNAPSHOT.jar` a fürthöz.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
    ```

    Cserélje le a `hdinsightclustername` elemet a fürt nevére. Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazda) nevét és jelszavát. A parancs feltölti a `hbaseapp-1.0-SNAPSHOT.jar` számára a `example/jars` a fürt elsődleges tárolási helyét.

5. Egy tábla használatával létrehozásához a `hbaseapp`, használja a következő parancsot:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
    ```

    Cserélje le a `hdinsightclustername` elemet a fürt nevére. Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazda) nevét és jelszavát.

    Ezzel a paranccsal létrejön nevű tábla **személyek** a HBase a HDInsight-fürtre. Ez a parancs nem szerepelnek a konzolablakban kimenetet.

6. Keresse meg a táblázat, használja a következő parancsot:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
    ```

    Cserélje le a `hdinsightclustername` elemet a fürt nevére. Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazda) nevét és jelszavát.

    Ez a parancs a `SearchByEmail` osztály egyetlen sort sem kereséséhez ahol a `contactinformation` oszlop termékcsalád és a `email` oszlopa tartalmazza a karakterláncot `contoso.com`. A következő eredményeket kell megjelennie:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Használatával **fabrikam.com** a a `-emailRegex` értéket adja vissza a felhasználóknak, akiknek **fabrikam.com** az e-mailek mezőben. Reguláris kifejezéseket is használják a kívánt keresőkifejezést. Például **^ r** értéket ad vissza e-mail címeket, amelyek "r" betűvel kezdődik.

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nincs eredményeit, illetve a Start-HBaseExample használata váratlan eredményekhez

Használja a `-showErr` paraméter a normál hiba (STDERR), a feladat futtatása során előállított megtekintéséhez.

## <a name="delete-the-table"></a>A tábla törlése

Amikor elkészült, a példában a, a következő törléséhez használja a **személyek** ebben a példában használt tábla:

__Az egy `ssh` munkamenet__:

`yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable`

__Az Azure PowerShell__:

`Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername`

## <a name="next-steps"></a>További lépések

[Ismerje meg az SQuirreL SQL használata HBase](apache-hbase-phoenix-squirrel-linux.md)
