---
title: Java HBase ügyfél – Azure HDInsight
description: Ismerje meg, az Apache Maven használata Java-alapú Apache HBase-alkalmazás létrehozása, majd telepítse az Azure HDInsight HBase.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: 677714487aac6e25a0505cce978792c76bb1cee4
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51016080"
---
# <a name="build-java-applications-for-apache-hbase"></a>Az Apache HBase Java-alkalmazások

Ismerje meg, hogyan hozhat létre egy [Apache HBase](http://hbase.apache.org/) alkalmazás Java-környezetben. Az Azure HDInsight HBase majd használni az alkalmazást.

A lépéseket, a jelen dokumentum-használat [Maven](http://maven.apache.org/) hozhat létre, és a projekt buildjének elkészítéséhez. Maven egy szoftverfrissítési projektmenedzsment és a szövegértést eszköz, amely lehetővé teszi, hogy a szoftver, a dokumentáció és a Java-projektek jelentéseket hozhat létre.

> [!NOTE]
> A jelen dokumentumban leírt lépések legutóbb tesztelt a HDInsight 3.6-ot.

> [!IMPORTANT]
> A dokumentum lépéseinek elvégzéséhez egy Linux-alapú HDInsight-fürt szükséges. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Követelmények

* [A Java platform JDK](https://aka.ms/azure-jdks) 8 vagy újabb.

    > [!NOTE]
    > A HDInsight 3.5-ös és újabb Java 8 van szükség. HDInsight korábbi verzióiban a Java 7 van szükség.

* [Maven 3](http://maven.apache.org/)

* [Egy Linux-alapú Azure HDInsight-fürtöt, a hbase-ben](apache-hbase-tutorial-get-started-linux.md#create-hbase-cluster)

## <a name="create-the-project"></a>A projekt létrehozása

1. A fejlesztési környezetben a parancssorban módosítsa a könyvtárat a helyet, ahol szeretné létrehozni a projektet, például `cd code\hbase`.

2. Használja a **mvn** parancsot, amely a Mavennel telepítve van, a keret létrehozásához a projekt létrehozásához.

    ```bash
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    > [!NOTE]
    > Ha a Powershellt használ, meg kell tenni a `-D` paramétereket a dupla idézőjelek között.
    >
    > `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=hbaseapp" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Ez a parancs létrehoz egy könyvtárat a neve megegyezik a **artifactID** paraméter (**hbaseapp** ebben a példában.) Ez a könyvtár a következő elemeket tartalmazza:

   * **pom.xml**: A projekt Hálózatiobjektum-modellt ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) tartalmazza a projekt felépítéséhez használt információkat és a konfiguráció részleteit.
   * **src**: A címtár, amely tartalmazza a **main/java/com/microsoft/példák** könyvtárat, ahol az alkalmazást hoz létre.

3. Törölje a `src/test/java/com/microsoft/examples/apptest.java` fájlt. Nem használható ebben a példában.

## <a name="update-the-project-object-model"></a>Frissítse a projekt Hálózatiobjektum-modellje

1. Szerkessze a `pom.xml` fájlt, és adja hozzá a következő kódot a `<dependencies>` szakaszban:

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

    Ez a szakasz azt jelzi, hogy kell-e a projekt **hbase-ügyfél** és **phoenix-core** összetevőket. A fordítás során a függőségeket a alapértelmezett Maven tárházból letöltődnek. Használhatja a [Maven központi tárházban keresési](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) további információ a függőség.

   > [!IMPORTANT]
   > A hbase-ügyfél verziószáma a HBase a HDInsight-fürt a megadott verziójának egyeznie kell. A következő táblázat segítségével keresse meg a megfelelő verziószámot.

   | HDInsight-fürt verziója | HBase-verzió használata |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3-as, 3.4-es, 3.5-ös és 3.6-os |1.1.2 |

    A HDInsight-verziók és -összetevők további információkért lásd: [Mik azok a különböző elérhető, a HDInsight Hadoop-összetevők](../hdinsight-component-versioning.md).

3. Adja hozzá a következő kódot a **pom.xml** fájlt. Ez a szöveg belül kell lennie a `<project>...</project>` a fájlt, a címkék között például `</dependencies>` és `</project>`.

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

    Ebben a szakaszban konfigurálja egy erőforrást (`conf/hbase-site.xml`), amely tartalmazza a konfigurációs adatait a hbase-hez.

   > [!NOTE]
   > Beállíthatja a konfigurációs értékek kód segítségével is. A Megjegyzések a `CreateTable` példa.

    Ez a szakasz is konfigurálja a [Maven fordító beépülő modul](http://maven.apache.org/plugins/maven-compiler-plugin/) és [Maven Shade beépülő modul](http://maven.apache.org/plugins/maven-shade-plugin/). A beépülő modul fordító fordítsa le a topológia szolgál. A beépülő modult árnyalatot licenc azonos átvitelszervezőpéldány-azonosítók a JAR-csomag, amely szerint a Maven megelőzése érdekében használatos. Ez a beépülő modul segítségével egy "ismétlődő licencfájlok" hiba megakadályozza a futási időben, a HDInsight-fürtön. Maven-shade-bővítménnyel rendelkező a `ApacheLicenseResourceTransformer` megvalósítási megakadályozza, hogy a hiba.

    A maven-shade-beépülő modul is az uber jar, amely tartalmazza az alkalmazás számára szükséges összes függőséget hoz létre.

4. Mentse a `pom.xml` fájlt.

5. Hozzon létre egy könyvtárat nevű `conf` a a `hbaseapp` könyvtár. Ez a könyvtár HBase való kapcsolódáshoz konfigurációs adatainak tárolására szolgál.

6. A következő parancs használatával másolja a HBase-beállításait az a HBase-fürt a `conf` könyvtár. Cserélje le `USERNAME` az SSH-bejelentkezési névvel. Cserélje le `CLUSTERNAME` a HDInsight-fürt nevére:

    ```bash
    scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
    ```

   További tájékoztatást `ssh` és `scp`, lásd: [az SSH használata a HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-application"></a>Az alkalmazás létrehozása

1. Nyissa meg a `hbaseapp/src/main/java/com/microsoft/examples` könyvtárat, és nevezze át az app.java fájlt `CreateTable.java`.

2. Nyissa meg a `CreateTable.java` fájlt, és cserélje le a meglévő tartalmát a következő szöveget:

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

    Ez a kód a **CreateTable** osztály, amely létrehoz egy tábla **személyek** és a egy előre meghatározott felhasználók való feltöltéséhez.

3. Mentse a `CreateTable.java` fájlt.

4. Az a `hbaseapp/src/main/java/com/microsoft/examples` könyvtárban hozzon létre egy fájlt `SearchByEmail.java`. A fájl tartalma legyen a következő szöveg:

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

    A **SearchByEmail** osztály használható e-mail-cím alapján sorok lekérdezéséhez. Mert az egy reguláris kifejezés szűrőt használ, megadhat egy karakterlánc vagy egy reguláris kifejezést az osztály használatakor.

5. Mentse a `SearchByEmail.java` fájlt.

6. Az a `hbaseapp/src/main/hava/com/microsoft/examples` könyvtárban hozzon létre egy fájlt `DeleteTable.java`. A fájl tartalma legyen a következő szöveg:

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

    Ez az osztály a HBase-táblákat létrehozni ebben a példában letiltásával és által létrehozott táblát elvetését megtisztítja a `CreateTable` osztály.

7. Mentse a `DeleteTable.java` fájlt.

## <a name="build-and-package-the-application"></a>Hozhat létre, és az alkalmazás becsomagolása

1. Az a `hbaseapp` könyvtár, használja az alábbi parancsot egy JAR-fájlt, amely tartalmazza az alkalmazást hozhat létre:

    ```bash
    mvn clean package
    ```

    Ez a parancs létrehozza, és csomagolja be a .jar-fájl az alkalmazás.

2. A parancs befejeződésekor a `hbaseapp/target` könyvtár tartalmaz egy fájlt `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]
   > A `hbaseapp-1.0-SNAPSHOT.jar` fájl az uber jar. Az alkalmazás futtatásához szükséges összes függőséget tartalmaz.


## <a name="upload-the-jar-and-run-jobs-ssh"></a>Töltse fel a fájlt, és futtasson feladatokat (SSH)

Az alábbi lépések az `scp` fájlt másolja az elsődleges átjárócsomóponthoz, a HBase a HDInsight-fürtön. A `ssh` parancs majd segítségével csatlakozhat a fürthöz, és a példa futtatása közvetlenül a központi csomóponton.

1. A fürt fájlt tölthet fel, használja a következő parancsot:

    ```bash
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

    Cserélje le `USERNAME` az SSH-bejelentkezési névvel. Cserélje le `CLUSTERNAME` a HDInsight-fürt nevére.

2. Ha csatlakozni szeretne a HBase-fürtnek, használja a következő parancsot:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Cserélje le `USERNAME` az SSH-bejelentkezési nevét. Cserélje le `CLUSTERNAME` a HDInsight-fürt nevére.

3. A Java-alkalmazás használatával egy HBase-tábla létrehozásához használja a következő parancsot:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Ez a parancs létrehoz egy HBase tábla **személyek**, és tölti fel az adatokat.

4. Keresse meg a táblában tárolt e-mail-címeket, használja a következő parancsot:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    A következő eredményeket jelenhet meg:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. A tábla törléséhez használja a következő parancsot:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Töltse fel a fájlt, és futtasson feladatokat (PowerShell)

Az alábbi lépéseket az Azure PowerShell használatával fájlt tölthet fel az alapértelmezett tároló, a HBase-fürtnek. HDInsight-parancsmagokat szolgálnak majd a példák távolról futtatni.

1. Miután telepítése és konfigurálása az Azure PowerShell-lel, hozzon létre egy fájlt `hbase-runner.psm1`. A fájl tartalma legyen a következő szöveg:

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

    Ez a fájl két modult tartalmaz:

   * **Adjon hozzá HDInsightFile** – fájlok feltöltése a fürt használatos
   * **Start-HBaseExample** – az a korábban létrehozott osztályok futtatása

2. Mentse a `hbase-runner.psm1` fájlt.

3. Nyisson meg egy új Azure PowerShell-ablakot, lépjen a `hbaseapp` könyvtárat, és futtassa a következő parancsot:

    ```powershell
    PS C:\ Import-Module c:\path\to\hbase-runner.psm1
    ```

    Módosítsa az elérési helyét az `hbase-runner.psm1` korábban létrehozott fájlt. Ezzel a paranccsal regisztrálja a modul az Azure PowerShell használatával.

4. A következő paranccsal töltse fel a `hbaseapp-1.0-SNAPSHOT.jar` a fürtön.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername
    ```

    Cserélje le a `hdinsightclustername` elemet a fürt nevére. Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) nevet és jelszót. A parancs feltölti az `hbaseapp-1.0-SNAPSHOT.jar` , a `example/jars` a fürt számára az elsődleges tárolási helyét.

5. Hozhat létre egy táblát a a `hbaseapp`, használja a következő parancsot:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername
    ```

    Cserélje le a `hdinsightclustername` elemet a fürt nevére. Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) nevet és jelszót.

    Ez a parancs létrehoz egy tábla **személyek** a HBase a HDInsight-fürtön. Ez a parancs nem jeleníti meg a kimenetet a konzolablakban.

6. Keresse meg a táblázat, használja a következő parancsot:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com
    ```

    Cserélje le a `hdinsightclustername` elemet a fürt nevére. Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) nevet és jelszót.

    Ez a parancs a `SearchByEmail` osztály keresse meg azokat a sorokat, ahol a `contactinformation` oszlopcsalád és a `email` oszlop tartalmazza a karakterláncot `contoso.com`. A következő eredményeket kell kapnia:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Használatával **fabrikam.com** számára a `-emailRegex` értéket adja vissza a felhasználóknak, akiknek **fabrikam.com** az e-mailek mezőben. Reguláris kifejezések a keresési kifejezést is használhatja. Ha például **^ r** értéket ad vissza e-mail-címeket, amelyek az "r" betűvel kezdődik.

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nincsenek eredmények vagy a Start-HBaseExample használatakor nem várt eredmények

Használja a `-showErr` paraméter megtekintéséhez a normál hiba (STDERR), amely a feladat futtatásakor jön létre.

## <a name="delete-the-table"></a>A tábla törlése

Amikor elkészült, a példában, használja a következő törli a **személyek** ebben a példában használt táblák:

__Az egy `ssh` munkamenet__:

`yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable`

__Az Azure PowerShell-lel__:

`Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername`

## <a name="next-steps"></a>További lépések

[SQuirreL SQL használata a hbase-ben](apache-hbase-phoenix-squirrel-linux.md)
