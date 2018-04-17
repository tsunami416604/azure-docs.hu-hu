---
title: Windows-alapú Azure hdinsight Java HBase-alkalmazás létrehozása |} Microsoft Docs
description: Útmutató Apache Maven segítségével összeállíthat egy Apache HBase Java-alapú alkalmazást, majd központilag telepítenie kell egy Windows-alapú Azure HDInsight-fürtöt.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f4a4e02-45ab-40dd-842b-3ec034f256c9
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 7ec84260a72b2745630087ceac24c8c00ff3f04f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-maven-to-build-java-applications-that-use-hbase-with-windows-based-hdinsight-hadoop"></a>A HBase és a Windows-alapú HDInsight (Hadoop) együttes használata Java-alkalmazások készítéséhez használja Maven
Ismerje meg, hogyan hozhat létre és build egy [Apache HBase](http://hbase.apache.org/) alkalmazás Apache Maven használatával Java nyelven. Ezután használja az alkalmazást az Azure HDInsight (Hadoop).

[Maven](http://maven.apache.org/) szoftver project management és a szövegértést eszköz használatával olyan szoftver, a dokumentáció és a Java-projektek a jelentésekre. Ebből a cikkből megismerheti, hogyan használhatja egy alapszintű Java-alkalmazás létrehozása, amely hoz létre, a lekérdezések, és törli az Azure HDInsight-fürtök egy HBase tábla.

> [!IMPORTANT]
> A jelen dokumentumban leírt lépések egy HDInsight-fürt által használt Windows igényelnek. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Követelmények
* [Java-platform JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 vagy újabb
* [Maven 3](http://maven.apache.org/)
* Egy Windows-alapú HDInsight-fürtöt, a hbase eszközzel

    > [!NOTE]
    > A jelen dokumentumban leírt lépések HDInsight fürt verzióival 3.2-es és 3.3-as lettek tesztelve. Példák a megadott alapértelmezett értékek vannak HDInsight 3.3 fürt.

## <a name="create-the-project"></a>A projekt létrehozása
1. A parancssorban a fejlesztési környezetet, módosítsa a könyvtárat a helyének például a projekt létrehozásához `cd code\hdinsight`.
2. Használja a **mvn** parancsot, amely Maven együtt települ, a állványok a projekt létrehozásához.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    A megadott névvel ezzel a paranccsal létrejön az aktuális helyen, egy könyvtárat a **artifactid szakaszát** paraméter (**hbaseapp** ebben a példában.) Ez a könyvtár a következő elemeket tartalmazza:

   * **pom.xml**: A projekt Object Model ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) és a projekt felépítéséhez használt információkat és konfigurációs részleteit tartalmazza.
   * **src**: tartalmazó könyvtárat a **main\java\com\microsoft\examples** könyvtárban, ahol meg fogja írni az alkalmazás.
3. Törölje a **src\test\java\com\microsoft\examples\apptest.java** fájlhoz, mert nem szerepel ebben a példában.

## <a name="update-the-project-object-model"></a>Frissítés a projekt Hálózatiobjektum-modellje
1. Szerkessze a **pom.xml** fájlt, és adja hozzá az alábbi kódot a `<dependencies>` szakasz:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Ez a szakasz azt ismerteti Maven, hogy szükséges-e a projekt **hbase-ügyfél** verzió **1.1.2**. Fordítási időben a függőség letölti az alapértelmezett Maven-tárházat. Használhatja a [Maven központi tárház keresési](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) további információt a függőség.

   > [!IMPORTANT]
   > A verziószámnak meg kell egyeznie a HBase a HDInsight-fürthöz megadott verzióját. Használja az alábbi táblázatban található a megfelelő verziószámot.
   >
   >

   | HDInsight-fürt verziószáma | A HBase-verziót kell használni |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 |1.1.2 |

    A HDInsight-verziókról és összetevők további információkért lásd: [Mik a különböző érhető el a HDInsight Hadoop-összetevők](hdinsight-component-versioning.md).
2. Ha egy HDInsight 3.3-fürtöt használ, a következő is hozzá kell adnia a `<dependencies>` szakasz:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>

    A függőség betölti a phoenix-alapvető összetevői, Hbase verziója által használt 1.1.x.
3. Adja hozzá a következő kódot a **pom.xml** fájlt. Ez a szakasz belül kell lennie a `<project>...</project>` címkék a fájlban, például közötti `</dependencies>` és `</project>`.

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
                    <source>1.7</source>
                    <target>1.7</target>
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

    A `<resources>` szakasz konfigurál egy erőforrást (**conf\hbase-site.xml**), amely a HBase konfigurációs információkat tartalmaz.

   > [!NOTE]
   > Megadhatja a konfigurációs értékeket keresztül kódot is. A Megjegyzések a **CreateTable** ennek az alábbi példa.
   >
   >

    Ez `<plugins>` szakasz konfigurálja a [Maven fordító beépülő modul](http://maven.apache.org/plugins/maven-compiler-plugin/) és [Maven árnyalatát beépülő modul](http://maven.apache.org/plugins/maven-shade-plugin/). A fordítóprogram beépülő modul segítségével lefordítani a topológia. A beépülő modul színárnyalat annak megelőzésére szolgál a JAR-csomagot, amely épül fel Maven licenc rejlő ismétlődést. Ez használható oka az, hogy az ismétlődő licencfájlok futási időben a HDInsight-fürt hibát okozhat. Maven-árnyalatát-beépülő modul a használatával a `ApacheLicenseResourceTransformer` megvalósítási megakadályozza, hogy ez a hiba.

    A maven-árnyalatát-beépülő modul hoz létre egy uber jar (vagy az fat jar) is, amely tartalmazza az alkalmazás által igényelt összes függősége.
4. Mentse a **pom.xml** fájlt.
5. Hozzon létre egy új könyvtárat nevű **conf** a a **hbaseapp** könyvtár. Az a **conf** könyvtár, hozzon létre egy fájlt **hbase-site.xml**. A fájl tartalmát az alábbiak használata:

        <?xml version="1.0"?>
        <?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
        <!--
        /**
          * Copyright 2010 The Apache Software Foundation
          *
          * Licensed to the Apache Software Foundation (ASF) under one
          * or more contributor license agreements.  See the NOTICE file
          * distributed with this work for additional information
          * regarding copyright ownership.  The ASF licenses this file
          * to you under the Apache License, Version 2.0 (the
          * "License"); you may not use this file except in compliance
          * with the License.  You may obtain a copy of the License at
          *
          *     http://www.apache.org/licenses/LICENSE-2.0
          *
          * Unless required by applicable law or agreed to in writing, software
          * distributed under the License is distributed on an "AS IS" BASIS,
          * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
          * See the License for the specific language governing permissions and
          * limitations under the License.
          */
        -->
        <configuration>
          <property>
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeeper0,zookeeper1,zookeeper2</value>
          </property>
          <property>
            <name>hbase.zookeeper.property.clientPort</name>
            <value>2181</value>
          </property>
        </configuration>

    Ez a fájl betöltése a HDInsight-fürtök a HBase konfigurációs használható.

   > [!NOTE]
   > A minimális hbase-site.xml fájl, és a HDInsight-fürthöz az operációs rendszer minimális beállításokat tartalmaz.

6. Mentse a **hbase-site.xml** fájlt.

## <a name="create-the-application"></a>Az alkalmazás létrehozása
1. Lépjen a **hbaseapp\src\main\java\com\microsoft\examples** könyvtár, és nevezze át a app.java fájl **CreateTable.java**.
2. Nyissa meg a **CreateTable.java** fájlt, és cserélje ki a meglévő tartalmát az alábbira:

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
            // The following sets the znode root for Linux-based HDInsight
            //config.set("zookeeper.znode.parent","/hbase-unsecure");

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

    Ez a **CreateTable** osztályt, amely létrehoz egy táblát nevű **személyek** , és feltöltheti olyan előre definiált felhasználók.
3. Mentse a **CreateTable.java** fájlt.
4. Az a **hbaseapp\src\main\java\com\microsoft\examples** könyvtár, hozzon létre egy új fájlt **SearchByEmail.java**. Ez a fájl tartalmát az alábbira használata:

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

            // Create a new regex filter
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

    A **SearchByEmail** osztály használható lekérdezze a sorok e-mail cím alapján. Reguláris kifejezés szűrőt használja, mert biztosíthat egy karakterlánc vagy egy reguláris kifejezést az osztály használata esetén.
5. Mentse a **SearchByEmail.java** fájlt.
6. Az a **hbaseapp\src\main\hava\com\microsoft\examples** könyvtár, hozzon létre egy új fájlt **DeleteTable.java**. Ez a fájl tartalmát az alábbira használata:

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

    Ez az osztály van ebben a példában tisztítási letiltásával és eldobása hozta létre a tábla a **CreateTable** osztály.
7. Mentse a **DeleteTable.java** fájlt.

## <a name="build-and-package-the-application"></a>Hozza létre, és az alkalmazás becsomagolása
1. Nyisson meg egy parancssort, és lépjen a **hbaseapp** könyvtár.
2. A következő paranccsal hozhat létre az alkalmazást tartalmazó JAR-fájlt:

        mvn clean package

    Ez törli az előző build összetevőihez, letölti az összes függőséget, amely már nincs telepítve, majd alapszik, és az alkalmazás a csomagokat.
3. A parancs befejeződésekor a **hbaseapp\target** directory nevű fájlt tartalmaz **hbaseapp-1.0-SNAPSHOT.jar**.

   > [!NOTE]
   > A **hbaseapp-1.0-SNAPSHOT.jar** fájl egy uber jar (más néven a fat jar), amely tartalmazza az alkalmazás futtatásához szükséges összes függősége.

## <a name="upload-the-jar-file-and-start-a-job"></a>Töltse fel a JAR-fájlra, és a feladat indítása
Számos módon feltölteni a fájlt a HDInsight-fürthöz, a [feltölteni az adatokat a HDInsight Hadoop-feladatok](hdinsight-upload-data.md). Az alábbi lépéseket az Azure PowerShell használata.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Miután telepítése és konfigurálása az Azure PowerShell, hozzon létre egy új fájlt **hbase-runner.psm1**. A fájl tartalmát az alábbiak használata:

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
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
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

    Ez a fájl két lehetővé tevő modulokat tartalmaz:

   * **Adja hozzá HDInsightFile** - használt HDInsight fájlok feltöltése
   * **Start-HBaseExample** - a korábban létrehozott osztályok futtatásához használt
2. Mentse a **hbase-runner.psm1** fájlt.
3. Nyisson meg egy új Azure PowerShell-ablakot, lépjen a **hbaseapp** könyvtár, és futtassa a következő parancsot.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Az elérési utat módosítsa a helyét a **hbase-runner.psm1** korábban létrehozott fájlt. Ez a modul az Azure PowerShell-munkamenetben regisztrál.
4. Az alábbi parancs segítségével töltse fel a **hbaseapp-1.0-SNAPSHOT.jar** a HDInsight-fürthöz.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Cserélje le **hdinsightclustername** a HDInsight-fürt nevét. A parancs feltölti a **hbaseapp-1.0-SNAPSHOT.jar** számára a **példa/JAR-fájlok kivételével** a HDInsight-fürthöz elsődleges tárolási helyét.
5. Miután a fájlok feltöltése, a következő kódot használja hozzon létre egy táblát az a **hbaseapp**:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Cserélje le **hdinsightclustername** a HDInsight-fürt nevét.

    Ezzel a paranccsal létrejön egy új tábla nevű **személyek** a HDInsight-fürtön. Ez a parancs nem szerepelnek a konzolablakban kimenetet.
6. Keresse meg a táblázat, használja a következő parancsot:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Cserélje le **hdinsightclustername** a HDInsight-fürt nevét.

    Ez a parancs a **SearchByEmail** osztály egyetlen sort sem kereséséhez ahol a **contactinformation** oszlop termékcsalád és a **e-mail** oszlopa tartalmazza a karakterláncot **contoso.com**. A következő eredményeket kell megjelennie:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Használatával **fabrikam.com** a a `-emailRegex` értéket adja vissza a felhasználóknak, akiknek **fabrikam.com** az e-mailek mezőben. Ez a keresés reguláris kifejezés alapú szűrő használatával valósul meg, mert is megadhat reguláris kifejezéseket, például a **^ r**, mely értéket ad vissza bejegyzéseket, ahol az e-mailt "r" betűvel kezdődik.

## <a name="delete-the-table"></a>A tábla törlése
Amikor elkészült, a példában a, a következő paranccsal az Azure PowerShell-munkamenetben törli a **személyek** ebben a példában használt tábla:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Cserélje le **hdinsightclustername** a HDInsight-fürt nevét.

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nincs eredményeit, illetve a Start-HBaseExample használata váratlan eredményekhez
Használja a `-showErr` paraméter a normál hiba (STDERR), a feladat futtatása során előállított megtekintéséhez.
