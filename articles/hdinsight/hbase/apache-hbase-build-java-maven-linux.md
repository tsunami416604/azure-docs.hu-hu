---
title: Az Apache Maven használata Azure HDInsight egy Java HBase-ügyfél
description: Ismerje meg, az Apache Maven használata Java-alapú Apache HBase-alkalmazás létrehozása, majd telepítse az Azure HDInsight HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: conceptual
ms.date: 04/16/2019
ms.openlocfilehash: a4c601e81390efa3bb53a6f07225bb6e939bc9bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726440"
---
# <a name="build-java-applications-for-apache-hbase"></a>Az Apache HBase Java-alkalmazások

Ismerje meg, hogyan hozhat létre egy [Apache HBase](https://hbase.apache.org/) alkalmazás Java-környezetben. Az Azure HDInsight HBase majd használni az alkalmazást.

A lépéseket, a jelen dokumentum-használat [Apache Maven](https://maven.apache.org/) hozhat létre, és a projekt buildjének elkészítéséhez. Maven egy szoftverfrissítési projektmenedzsment és a szövegértést eszköz, amely lehetővé teszi, hogy a szoftver, a dokumentáció és a Java-projektek jelentéseket hozhat létre.

## <a name="prerequisites"></a>Előfeltételek

* A HDInsight Apache HBase-fürtöt. Lásd: [Apache HBase használatának első lépései](./apache-hbase-tutorial-get-started-linux.md).

* [Java fejlesztői készlet (JDK) 8-as verzió](https://aka.ms/azure-jdks).

* [Az Apache Maven](https://maven.apache.org/download.cgi) megfelelően [telepített](https://maven.apache.org/install.html) Apache megfelelően.  Maven egy projektet a Java-projektek rendszert hozhat létre.

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Ha a PowerShell használatával, akkor a [AZ modul](https://docs.microsoft.com/powershell/azure/overview).

* Egy szövegszerkesztő. Ez a cikk a Microsoft Notepad használja.

> [!IMPORTANT]  
> Az Azure PowerShell-parancsmagok [Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) és [Get-AzHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) jelenleg nem működnek a mikor [biztonságos átvitelre](../../storage/common/storage-require-secure-transfer.md) engedélyezve van a tárfiók .

## <a name="test-environment"></a>Tesztkörnyezet
Az ebben a cikkben használt környezet a Windows 10 rendszerű számítógép volt.  A parancsok végrehajtódtak parancsot, és a különböző fájlok is szerkeszthetők a Jegyzettömb alkalmazásban. Ennek megfelelően módosítsa a környezetnek.

Egy parancssorból adja meg a munkakörnyezet létrehozásához az alábbi parancsokat:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Hozzon létre egy Maven-projektet

1. Adja meg a következő parancsot egy nevű Maven-projekt létrehozása **hbaseapp**:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Ez a parancs létrehoz egy könyvtárat nevű `hbaseapp` az aktuális helyen, amely tartalmaz egy alapszintű Maven-projektet. A második parancs módosítja a munkakönyvtárból abba `hbaseapp`. A harmadik parancs létrehoz egy új könyvtárat `conf`, amelyek később fogja használni. A `hbaseapp` könyvtár a következő elemeket tartalmazza:

    * `pom.xml`:  A projekt Object Model ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) tartalmazza a projekt felépítéséhez használt információkat és a konfiguráció részleteit.
    * `src\main\java\com\microsoft\examples`: Az alkalmazás kódját tartalmazza.
    * `src\test\java\com\microsoft\examples`: Az alkalmazás tesztek tartalmazza.

2. Távolítsa el a generált példakód. Törölje a létrehozott tesztelési és alkalmazásfájlok `AppTest.java`, és `App.java` írja be az alábbi parancsokat:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Frissítse a projekt Hálózatiobjektum-modellje

A pom.xml fájlt a teljes körű referenciáért lásd: https://maven.apache.org/pom.html.  Nyissa meg `pom.xml` az alábbi parancs beírásával:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Függőségek hozzáadása

A `pom.xml`, adja hozzá a következő szöveget a `<dependencies>` szakaszban:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Ez a szakasz azt jelzi, hogy kell-e a projekt **hbase-ügyfél** és **phoenix-core** összetevőket. A fordítás során a függőségeket a alapértelmezett Maven tárházból letöltődnek. Használhatja a [Maven központi tárházban keresési](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) további információ a függőség.

> [!IMPORTANT]  
> A hbase-ügyfél verziószáma meg kell egyeznie az Apache HBase a HDInsight-fürt a megadott verzióját. A következő táblázat segítségével keresse meg a megfelelő verziószámot.

| HDInsight-fürt verziója | Az Apache HBase verziója |
| --- | --- |
| 3.6 | 1.1.2 |
| 4.0 | 2.0.0 |

A HDInsight-verziók és -összetevők további információkért lásd: [Mik azok a különböző HDInsight elérhető az Apache Hadoop-összetevő](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Konfigurace buildu

Maven beépülő modulok lehetővé teszik a project build fázisa testreszabása. Ez a szakasz segítségével adja hozzá a beépülő modulok, erőforrások és más build-konfigurációs beállítások.

Adja hozzá a következő kódot a `pom.xml` fájlt, majd mentse és zárja be a fájlt. Ez a szöveg belül kell lennie a `<project>...</project>` a fájlt, a címkék között például `</dependencies>` és `</project>`.

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
                <version>3.8.0</version>
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

Ebben a szakaszban konfigurálja egy erőforrást (`conf/hbase-site.xml`), amely tartalmazza a konfigurációs adatait a hbase-hez.

> [!NOTE]  
> Beállíthatja a konfigurációs értékek kód segítségével is. A Megjegyzések a `CreateTable` példa.

Ez a szakasz is konfigurálja a [Apache Maven fordító beépülő modul](https://maven.apache.org/plugins/maven-compiler-plugin/) és [Apache Maven Shade beépülő modul](https://maven.apache.org/plugins/maven-shade-plugin/). A beépülő modul fordító fordítsa le a topológia szolgál. A beépülő modult árnyalatot licenc azonos átvitelszervezőpéldány-azonosítók a JAR-csomag, amely szerint a Maven megelőzése érdekében használatos. Ez a beépülő modul segítségével egy "ismétlődő licencfájlok" hiba megakadályozza a futási időben, a HDInsight-fürtön. Maven-shade-bővítménnyel rendelkező a `ApacheLicenseResourceTransformer` megvalósítási megakadályozza, hogy a hiba.

A maven-shade-beépülő modul is az uber jar, amely tartalmazza az alkalmazás számára szükséges összes függőséget hoz létre.

### <a name="download-the-hbase-sitexml"></a>Töltse le a hbase-site.xml

A következő parancs használatával másolja a HBase-beállításait az a HBase-fürt a `conf` könyvtár. Cserélje le `CLUSTERNAME` a HDInsight-fürt neve, és írja be a parancsot:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Az alkalmazás létrehozása

### <a name="implement-a-createtable-class"></a>CreateTable osztályok megvalósítása

Adja meg a parancsot hozhat létre, és nyisson meg egy új fájlt az alábbi `CreateTable.java`. Válassza ki **Igen** , hozzon létre egy új fájlt a parancssorba.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Ezután másolja és illessze be az alábbi java-kódot az új fájlt. Zárja be a fájlt.

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

Ez a kód a `CreateTable` osztály, amely létrehoz egy tábla `people` és a egy előre meghatározott felhasználók való feltöltéséhez.

### <a name="implement-a-searchbyemail-class"></a>SearchByEmail osztályok megvalósítása

Adja meg a parancsot hozhat létre, és nyisson meg egy új fájlt az alábbi `SearchByEmail.java`. Válassza ki **Igen** , hozzon létre egy új fájlt a parancssorba.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Ezután másolja és illessze be az alábbi java-kódot az új fájlt. Zárja be a fájlt.

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

A `SearchByEmail` osztály használható e-mail-cím alapján sorok lekérdezéséhez. Mert az egy reguláris kifejezés szűrőt használ, megadhat egy karakterlánc vagy egy reguláris kifejezést az osztály használatakor.

### <a name="implement-a-deletetable-class"></a>DeleteTable osztályok megvalósítása

Adja meg a parancsot hozhat létre, és nyisson meg egy új fájlt az alábbi `DeleteTable.java`. Válassza ki **Igen** , hozzon létre egy új fájlt a parancssorba.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Ezután másolja és illessze be az alábbi java-kódot az új fájlt. Zárja be a fájlt.

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

A `DeleteTable` osztály megtisztítja a HBase-táblákat létrehozni ebben a példában letiltásával és eldobása által létrehozott táblát a `CreateTable` osztály.

## <a name="build-and-package-the-application"></a>Hozhat létre, és az alkalmazás becsomagolása

1. Az a `hbaseapp` könyvtár, használja az alábbi parancsot egy JAR-fájlt, amely tartalmazza az alkalmazást hozhat létre:

    ```cmd
    mvn clean package
    ```

    Ez a parancs létrehozza, és csomagolja be a .jar-fájl az alkalmazás.

2. A parancs befejeződésekor a `hbaseapp/target` könyvtár tartalmaz egy fájlt `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]  
   > A `hbaseapp-1.0-SNAPSHOT.jar` fájl az uber jar. Az alkalmazás futtatásához szükséges összes függőséget tartalmaz.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Töltse fel a fájlt, és futtasson feladatokat (SSH)

Az alábbi lépések az `scp` fájlt másolja az elsődleges átjárócsomóponthoz, az Apache HBase a HDInsight-fürtön. A `ssh` parancs majd segítségével csatlakozhat a fürthöz, és a példa futtatása közvetlenül a központi csomóponton.

1. Töltse fel a fájlt a fürthöz. Cserélje le `CLUSTERNAME` a HDInsight-fürt neve, és írja be a következő parancsot:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Csatlakozhat a HBase-fürtnek. Cserélje le `CLUSTERNAME` a HDInsight-fürt neve, és írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

 3. A Java-alkalmazás használatával egy HBase-tábla létrehozásához használja a következő parancsot az Open ssh kapcsolat:

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

Az alábbi lépéseket az Azure PowerShell használata [AZ modul](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) fájlt tölthet fel az alapértelmezett tároló az Apache HBase-fürtnek. HDInsight-parancsmagokat szolgálnak majd a példák távolról futtatni.

1. Miután telepítése és konfigurálása a AZ modult, hozzon létre egy fájlt `hbase-runner.psm1`. A fájl tartalma legyen a következő szöveg:

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

   * **Adjon hozzá HDInsightFile** – fájlok feltöltése a fürt használatos
   * **Start-HBaseExample** – az a korábban létrehozott osztályok futtatása

2. Mentse a `hbase-runner.psm1` fájlt a `hbaseapp` könyvtár.

3. A modulok regisztrálása az Azure PowerShell használatával. Nyisson meg egy új Azure PowerShell-ablakot, és cserélje le az alábbi parancsot szerkesztéséhez `CLUSTERNAME` a fürt nevére. Ezután adja meg a következő parancsokat:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. A következő paranccsal töltse fel a `hbaseapp-1.0-SNAPSHOT.jar` a fürtön.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) nevet és jelszót. A parancs feltölti az `hbaseapp-1.0-SNAPSHOT.jar` , a `example/jars` a fürt számára az elsődleges tárolási helyét.

5. Hozhat létre egy táblát a a `hbaseapp`, használja a következő parancsot:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) nevet és jelszót.

    Ez a parancs létrehoz egy tábla **személyek** a HBase a HDInsight-fürtön. Ez a parancs nem jelenik meg semmilyen kimenetet a konzolablakban.

6. Keresse meg a táblázat, használja a következő parancsot:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Amikor a rendszer kéri, adja meg a fürt bejelentkezési (rendszergazdai) nevet és jelszót.

    Ez a parancs a `SearchByEmail` osztály keresse meg azokat a sorokat, ahol a `contactinformation` oszlopcsalád és a `email` oszlop tartalmazza a karakterláncot `contoso.com`. A következő eredményeket kell kapnia:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Használatával **fabrikam.com** számára a `-emailRegex` értéket adja vissza a felhasználóknak, akiknek **fabrikam.com** az e-mailek mezőben. Reguláris kifejezések a keresési kifejezést is használhatja. Ha például **^ r** értéket ad vissza e-mail-címeket, amelyek az "r" betűvel kezdődik.

7. A tábla törléséhez használja a következő parancsot:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nincsenek eredmények vagy a Start-HBaseExample használatakor nem várt eredmények

Használja a `-showErr` paraméter megtekintéséhez a normál hiba (STDERR), amely a feladat futtatásakor jön létre.

## <a name="next-steps"></a>További lépések

[Az Apache HBase SQuirreL SQL használata](apache-hbase-phoenix-squirrel-linux.md)
