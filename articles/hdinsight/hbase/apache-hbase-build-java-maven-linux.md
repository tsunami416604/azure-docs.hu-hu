---
title: Java HBase ügyfél létrehozása az Azure HDInsighthoz az Apache Maven segítségével
description: Ismerje meg, hogyan hozhat létre egy Java-alapú Apache HBase alkalmazást az Apache Maven használatával, majd telepítheti azt a HBase szolgáltatásba az Azure HDInsight-on.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/24/2019
ms.openlocfilehash: 3e9b23ce450e45dfedcee8b20e09b1c2b52b6e68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495782"
---
# <a name="build-java-applications-for-apache-hbase"></a>Java-alkalmazások készítése Apache HBase-hez

Ismerje meg, hogyan hozhat létre [Apache HBase](https://hbase.apache.org/) alkalmazást Java-ban. Ezután használja az alkalmazást a HBase az Azure HDInsight.

A jelen dokumentum lépései az [Apache Maven](https://maven.apache.org/) segítségével hozzák létre és építik fel a projektet. Maven egy szoftver projekt menedzsment és megértés eszköz, amely lehetővé teszi, hogy építsenek szoftver, dokumentáció, és a jelentések a Java projektek.

## <a name="prerequisites"></a>Előfeltételek

* Apache HBase-fürt a HDInsighton. Lásd: [Első lépések az Apache HBase-lel.](./apache-hbase-tutorial-get-started-linux.md)

* [Java Developer Kit (JDK) 8-as verzió](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) megfelelően [telepítve](https://maven.apache.org/install.html) szerint Apache.  Maven egy projekt épít rendszer Java projektek.

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](../hdinsight-hadoop-linux-use-ssh-unix.md)

* A PowerShell használata esetén szüksége lesz az [AZ-modulra.](https://docs.microsoft.com/powershell/azure/overview)

* Egy szövegszerkesztő. Ez a cikk a Microsoft Notepad programot használja.

## <a name="test-environment"></a>Vizsgálati környezet

A cikkhez használt környezet egy Windows 10-et futtató számítógép volt.  A parancsok végrehajtása a parancssorban történt, és a különböző fájlokat a Jegyzettömbbel szerkesztették. Módosítsa ennek megfelelően a környezethez.

A parancssorból adja meg az alábbi parancsokat a munkakörnyezet létrehozásához:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

1. Írja be a következő parancsot egy **hbaseapp**nevű Maven projekt létrehozásához:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Ez a parancs `hbaseapp` létrehoz egy könyvtárat, amelyet az aktuális helyen neveznek el, és amely egy alapvető Maven-projektet tartalmaz. A második parancs a `hbaseapp`munkakönyvtárat . A harmadik parancs új `conf`könyvtárat hoz létre, amelyet később fog használni. A `hbaseapp` könyvtár a következő elemeket tartalmazza:

    * `pom.xml`: A Project Object Model[(POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)a projekt felépítéséhez használt információkat és konfigurációs részleteket tartalmazza.
    * `src\main\java\com\microsoft\examples`: Tartalmazza az alkalmazás kódját.
    * `src\test\java\com\microsoft\examples`: Az alkalmazás tesztjeit tartalmazza.

2. Távolítsa el a létrehozott példakódot. Törölje a létrehozott teszt- `AppTest.java`és `App.java` alkalmazásfájlokat, és írja be az alábbi parancsokat:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>A projektobjektum-modell frissítése

A pom.xml fájl teljes körű https://maven.apache.org/pom.htmlhivatkozását lásd: .  Nyissa `pom.xml` meg az alábbi parancs megadásával:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Függőségek hozzáadása

A `pom.xml`alkalmazásban adja hozzá `<dependencies>` a következő szöveget a szakaszba:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Ez a szakasz azt jelzi, hogy a projektnek **hbase-client** és **phoenix-core** összetevőkre van szüksége. Fordításkor ezek a függőségek az alapértelmezett Maven-tárházból töltődnek le. A [Maven központi tárház keresés](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) segítségével többet megtudhat erről a függőségről.

> [!IMPORTANT]  
> A hbase-client verziószámának meg kell egyeznie az Apache HBase HDInsight-fürthöz mellékelt verziójával. Az alábbi táblázat segítségével megkeresheti a megfelelő verziószámot.

| HDInsight-fürt verziója | Az Apache HBase által használandó verzió |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

A HDInsight-verziókról és -összetevőkről a [HdInsight által elérhető különböző Apache Hadoop-összetevők](../hdinsight-component-versioning.md)című témakörben talál további információt.

### <a name="build-configuration"></a>Konfiguráció létrehozása

A Maven beépülő modulok lehetővé teszik a projekt építési szakaszainak testreszabását. Ez a szakasz beépülő modulok, erőforrások és egyéb build-konfigurációs beállítások hozzáadására szolgál.

Adja hozzá a `pom.xml` következő kódot a fájlhoz, majd mentse és zárja be a fájlt. Ennek a szövegnek `<project>...</project>` a fájl címkéiközött `</dependencies>` `</project>`kell lennie, például a és a között.

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
                <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
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

Ez a szakasz konfigurálja a HBase konfigurációs adatait tartalmazó erőforrást (`conf/hbase-site.xml`).

> [!NOTE]  
> A konfigurációs értékeket kódsegítségével is beállíthatja. Tekintse meg a `CreateTable` példában található megjegyzéseket.

Ez a rész az [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) és az [Apache Maven Shade Plugin -t](https://maven.apache.org/plugins/maven-shade-plugin/)is konfigurálja. A fordító beépülő modulja a topológia fordítására szolgál. Az árnyékbeépülő beépülő modul a Maven által készített JAR csomag licencduplikálásának megakadályozására szolgál. Ez a beépülő modul a HDInsight-fürt futási idején "ismétlődő licencfájlok" hibájának megelőzésére szolgál. A maven-shade-plugin `ApacheLicenseResourceTransformer` használata a megvalósítással megakadályozza a hibát.

A maven-shade-plugin is létrehoz egy uber jar, amely tartalmazza az alkalmazás által igényelt összes függőséget.

### <a name="download-the-hbase-sitexml"></a>A hbase-site.xml fájl letöltése

A következő paranccsal másolja a HBase konfigurációt `conf` a HBase fürtből a könyvtárba. Cserélje `CLUSTERNAME` le a HDInsight-fürt nevét, majd írja be a parancsot:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Az alkalmazás létrehozása

### <a name="implement-a-createtable-class"></a>CreateTable osztály megvalósítása

Új fájl létrehozásához és megnyitásához `CreateTable.java`írja be az alábbi parancsot. Új fájl létrehozásához válassza az **Igen** lehetőséget a kérdésnél.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Ezután másolja be az alábbi java kódot az új fájlba. Ezután zárja be a fájlt.

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

Ez a `CreateTable` kód az az osztály, amely létrehoz egy nevű `people` táblát, és feltölti azt néhány előre definiált felhasználóval.

### <a name="implement-a-searchbyemail-class"></a>SearchByEmail-osztály megvalósítása

Új fájl létrehozásához és megnyitásához `SearchByEmail.java`írja be az alábbi parancsot. Új fájl létrehozásához válassza az **Igen** lehetőséget a kérdésnél.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Ezután másolja be az alábbi java kódot az új fájlba. Ezután zárja be a fájlt.

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

Az `SearchByEmail` osztály segítségével e-mail-cím szerint lehet sorokat kérdezni. Mivel reguláris kifejezésszűrőt használ, az osztály használatakor karakterláncot vagy reguláris kifejezést adhat meg.

### <a name="implement-a-deletetable-class"></a>DeleteTable osztály megvalósítása

Új fájl létrehozásához és megnyitásához `DeleteTable.java`írja be az alábbi parancsot. Új fájl létrehozásához válassza az **Igen** lehetőséget a kérdésnél.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Ezután másolja be az alábbi java kódot az új fájlba. Ezután zárja be a fájlt.

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

Az `DeleteTable` osztály törli a példában létrehozott HBase táblákat az `CreateTable` osztály által létrehozott tábla letiltásával és eldobásával.

## <a name="build-and-package-the-application"></a>Az alkalmazás létrehozása és csomagolása

1. A `hbaseapp` könyvtárból a következő paranccsal hozzon létre egy JAR-fájlt, amely az alkalmazást tartalmazza:

    ```cmd
    mvn clean package
    ```

    Ez a parancs az alkalmazást .jar fájlba építi és csomagolja.

2. Amikor a parancs befejeződik, a `hbaseapp/target` `hbaseapp-1.0-SNAPSHOT.jar`könyvtár egy .

   > [!NOTE]  
   > A `hbaseapp-1.0-SNAPSHOT.jar` fájl egy uber jar. Az alkalmazás futtatásához szükséges összes függőséget tartalmazza.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Töltse fel a JAR-t és futtasson feladatokat (SSH)

A következő `scp` lépésekkel másolja a JAR az elsődleges fő csomópont az Apache HBase HDInsight-fürtön. A `ssh` parancs ezután a fürthöz való csatlakozásra és a példa közvetlenül a főcsomópontra való futtatására szolgál.

1. Töltse fel az üveget a fürtbe. Cserélje `CLUSTERNAME` le a HDInsight-fürt nevét, majd írja be a következő parancsot:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Csatlakozzon a HBase fürthöz. Cserélje `CLUSTERNAME` le a HDInsight-fürt nevét, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. HBase-tábla Java alkalmazással történő létrehozásához használja a következő parancsot a nyílt ssh kapcsolatban:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Ez a parancs létrehoz egy **személyek**nevű HBase táblát, és feltölti azt adatokkal.

4. A táblázatban tárolt e-mail címek kereséséhez használja a következő parancsot:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    A következő eredményeket kapja:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. A táblázat törléséhez használja a következő parancsot:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Töltse fel a JAR-t és futtasson feladatokat (PowerShell)

A következő lépések az Azure PowerShell [AZ modul](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) segítségével töltse fel a JAR az Apache HBase-fürt alapértelmezett tárolójába. A HDInsight-parancsmagok ezután a példák távoli futtatására szolgálnak.

1. Az AZ modul telepítése és konfigurálása `hbase-runner.psm1`után hozzon létre egy . A fájl tartalma legyen a következő szöveg:

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
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
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
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
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
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
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

   * **Add-HDInsightFile** - fájlok feltöltésére szolgál a fürtbe
   * **Start-HBaseExample** - a korábban létrehozott osztályok futtatásához használható

2. Mentse `hbase-runner.psm1` a fájlt `hbaseapp` a könyvtárba.

3. Regisztrálja a modulokat az Azure PowerShell segítségével. Nyisson meg egy új Azure PowerShell-ablakot, és az alábbi parancsot a fürt nevének lecserélésével. `CLUSTERNAME` Ezután írja be a következő parancsokat:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. A következő paranccsal `hbaseapp-1.0-SNAPSHOT.jar` töltse fel a fürtbe.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) nevét és jelszavát. A parancs feltölti `hbaseapp-1.0-SNAPSHOT.jar` `example/jars` a fürt elsődleges tárolójának helyére.

5. Ha a segítségével `hbaseapp`szeretne táblázatot létrehozni, használja a következő parancsot:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) nevét és jelszavát.

    Ez a parancs létrehoz egy **személyek** nevű táblát a HBase-ben a HDInsight-fürtön. Ez a parancs nem jelenít meg kimenetet a konzolablakban.

6. A táblázatban szereplő bejegyzések kereséséhez használja a következő parancsot:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) nevét és jelszavát.

    Ez a `SearchByEmail` parancs az osztály segítségével `contactinformation` keresi azokat `email` a sorokat, amelyekben az oszlopcsalád és az oszlop tartalmazza a karakterláncot. `contoso.com` A következő eredményeket kell kapnia:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Az érték `-emailRegex` hez **fabrikam.com** használatával az e-mail mezőben **fabrikam.com** felhasználókat adja vissza. A reguláris kifejezéseket keresési kifejezésként is használhatja. **A(z) ^r** például az "r" betűvel kezdődő e-mail címeket adja vissza.

7. A táblázat törléséhez használja a következő parancsot:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nincs eredmény vagy váratlan eredmény a Start-HBaseExample használatakor

A `-showErr` paraméter segítségével tekintse meg a szabványos hiba (STDERR), amely a feladat futtatása közben keletkezik.

## <a name="next-steps"></a>További lépések

[Az SQLLine használata az Apache HBase alkalmazással](apache-hbase-query-with-phoenix.md)
