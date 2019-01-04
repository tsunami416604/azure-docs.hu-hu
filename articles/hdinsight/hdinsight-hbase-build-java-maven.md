---
title: A Windows-alapú Azure HDInsight Java HBase-alkalmazás létrehozása
description: Ismerje meg, az Apache Maven használata Java-alapú Apache HBase-alkalmazás létrehozása, majd telepítheti a Windows-alapú Azure HDInsight-fürthöz.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: ea2fe0f7e326db00a63529c0279c9c15d30c744c
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744819"
---
# <a name="use-apache-maven-to-build-java-applications-that-use-apache-hbase-with-windows-based-hdinsight-apache-hadoop"></a>Az Apache Maven használata Java-alkalmazások, amelyek Apache HBase használata a Windows-alapú HDInsight (Apache Hadoop)
Ismerje meg, hogyan hozhat létre, és hozhat létre egy [Apache HBase](https://hbase.apache.org/) alkalmazás javában az Apache Maven segítségével. Ezután használja az alkalmazást az Azure HDInsight (Apache Hadoop).

[Az Apache Maven](https://maven.apache.org/) olyan szoftver project management és a szövegértést eszköz, amely lehetővé teszi, hogy a szoftver, a dokumentáció és a Java-projektek jelentéseket hozhat létre. Ebből a cikkből megismerheti, hogyan hozzon létre egy egyszerű Java-alkalmazást hoz létre, lekérdezések, és törli az Azure HDInsight-fürtön egy HBase-tábla használatával.

> [!IMPORTANT]  
> A jelen dokumentumban leírt lépések szükség egy HDInsight-fürt által használt Windows. A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="requirements"></a>Követelmények
* [A Java platform JDK](https://aka.ms/azure-jdks) 7 vagy újabb verzió
* [Apache Maven](https://maven.apache.org/)
* Egy Windows-alapú HDInsight-fürtöt, a hbase-ben

    > [!NOTE]  
    > A jelen dokumentumban leírt lépések fürtverziók HDInsight 3.2-es és 3.3-as teszteltük. Példák a megadott alapértelmezett értékek vannak a HDInsight 3.3-as fürt.

## <a name="create-the-project"></a>A projekt létrehozása
1. A fejlesztési környezetben a parancssorban módosítsa a könyvtárat a helyet, ahol szeretné létrehozni a projektet, például `cd code\hdinsight`.
2. Használja a **mvn** parancsot, amely a Mavennel telepítve van, a keret létrehozásához a projekt létrehozásához.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Ez a parancs létrehoz egy könyvtárat az aktuális helyen, által megadott név a **artifactID** paraméter (**hbaseapp** ebben a példában.) Ez a könyvtár a következő elemeket tartalmazza:

   * **pom.xml**:  A projekt Object Model ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) tartalmazza a projekt felépítéséhez használt információkat és a konfiguráció részleteit.
   * **src**: A könyvtár, amely tartalmazza a **main\java\com\microsoft\examples** könyvtárat, ahol az alkalmazás fogja készíthet.
3. Törölje a **src\test\java\com\microsoft\examples\apptest.java** fájlhoz, mert nem szerepel ebben a példában.

## <a name="update-the-project-object-model"></a>Frissítse a projekt Hálózatiobjektum-modellje
1. Szerkessze a **pom.xml** fájlt, és adja hozzá a következő kódot a `<dependencies>` szakaszban:

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

    Ez a szakasz arra utasítja a Maven, hogy szükséges-e a projekt **hbase-ügyfél** verzió **1.1.2**. A fordítás során a függőség az alapértelmezett Maven tárházból letölti. Használhatja a [Apache Maven központi tárházban keresési](https://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) további információ a függőség.

   > [!IMPORTANT]  
   > A verziószámnak meg kell egyeznie a HBase a HDInsight-fürt a megadott verzióját. A következő táblázat segítségével keresse meg a megfelelő verziószámot.
   >
   >

   | HDInsight-fürt verziója | HBase-verzió használata |
   | --- | --- |
   | 3.2 |0.98.4-hadoop2 |
   | 3.3 |1.1.2 |

    A HDInsight-verziók és -összetevők további információkért lásd: [Mik azok a különböző HDInsight elérhető az Apache Hadoop-összetevő](hdinsight-component-versioning.md).
2. Ha a HDInsight 3.3-as fürtöt használ, a következő is hozzá kell adnia a `<dependencies>` szakaszban:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>

    A függőség betölti a phoenix-alapösszetevők, Hbase-verzió által használt 1.1.x.
3. Adja hozzá a következő kódot a **pom.xml** fájlt. Ez a szakasz belül kell lennie a `<project>...</project>` a fájlt, a címkék között például `</dependencies>` és `</project>`.

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

    A `<resources>` szakaszban konfigurálja egy erőforrást (**conf\hbase-site.xml**), amely tartalmazza a konfigurációs adatait a hbase-hez.

   > [!NOTE]  
   > Beállíthatja a konfigurációs értékek kód segítségével is. A Megjegyzések a **CreateTable** ehhez a következő példában.
   >
   >

    Ez `<plugins>` szakaszban konfigurálja a [Apache Maven fordító beépülő modul](https://maven.apache.org/plugins/maven-compiler-plugin/) és [Apache Maven Shade beépülő modul](https://maven.apache.org/plugins/maven-shade-plugin/). A beépülő modul fordító fordítsa le a topológia szolgál. A beépülő modult árnyalatot licenc azonos átvitelszervezőpéldány-azonosítók a JAR-csomag, amely szerint a Maven megelőzése érdekében használatos. Ez használható oka az, hogy az ismétlődő licencfájlok futási időben, a HDInsight-fürtön hibát okozhat. Maven-shade-bővítménnyel rendelkező a `ApacheLicenseResourceTransformer` megvalósítási megakadályozza, hogy ezt a hibát.

    A maven-shade-beépülő modul is hoz létre, az uber jar (vagy az fat jar), amely tartalmazza az alkalmazás számára szükséges összes függőséget.
4. Mentse a **pom.xml** fájlt.
5. Hozzon létre egy új könyvtárat nevű **conf** a a **hbaseapp** könyvtár. Az a **conf** könyvtárban hozzon létre egy fájlt **hbase-site.xml**. A fájl tartalmát a következő használja:

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
          *     https://www.apache.org/licenses/LICENSE-2.0
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

    Ez a fájl betöltése egy HDInsight-fürt konfigurációját a HBase használható.

   > [!NOTE]  
   > Ez egy minimális hbase-site.xml fájlt, és a HDInsight-fürtöt operációs rendszer nélküli helyreállításra minimális beállításait tartalmazza.

6. Mentse a **hbase-site.xml** fájlt.

## <a name="create-the-application"></a>Az alkalmazás létrehozása
1. Nyissa meg a **hbaseapp\src\main\java\com\microsoft\examples** könyvtárat, és nevezze át az app.java fájlt **CreateTable.java**.
2. Nyissa meg a **CreateTable.java** fájlt, és cserélje le a meglévő tartalmát az alábbira:

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

    Ez a **CreateTable** osztály, amely létrehoz egy tábla **személyek** és a egy előre meghatározott felhasználók való feltöltéséhez.
3. Mentse a **CreateTable.java** fájlt.
4. Az a **hbaseapp\src\main\java\com\microsoft\examples** könyvtárban hozzon létre egy új fájlt **SearchByEmail.java**. Ez a fájl tartalmát használja a következő kódot:

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

    A **SearchByEmail** osztály használható e-mail-cím alapján sorok lekérdezéséhez. Mert az egy reguláris kifejezés szűrőt használ, megadhat egy karakterlánc vagy egy reguláris kifejezést az osztály használatakor.
5. Mentse a **SearchByEmail.java** fájlt.
6. Az a **hbaseapp\src\main\hava\com\microsoft\examples** könyvtárban hozzon létre egy új fájlt **DeleteTable.java**. Ez a fájl tartalmát használja a következő kódot:

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

    Ez az osztály az ebben a példában tisztítási letiltásával és eldobása által létrehozott táblát a **CreateTable** osztály.
7. Mentse a **DeleteTable.java** fájlt.

## <a name="build-and-package-the-application"></a>Hozhat létre, és az alkalmazás becsomagolása
1. Nyisson meg egy parancssort, és lépjen a **hbaseapp** könyvtár.
2. Használja a következő parancsot egy JAR-fájlt, amely tartalmazza az alkalmazást hozhat létre:

        mvn clean package

    Ez törli a bármely korábbi build-összetevőket, letölti a függőségek, amelyek még nem telepítette, majd összeállít és csomagok, az alkalmazás.
3. A parancs befejeződésekor a **hbaseapp\target** könyvtár tartalmaz egy fájlt **hbaseapp – 1.0-SNAPSHOT.jar**.

   > [!NOTE]  
   > A **hbaseapp – 1.0-SNAPSHOT.jar** fájl az uber jar (más néven egy fat jar), amely tartalmazza az alkalmazás futtatásához szükséges összes függőséget.

## <a name="upload-the-jar-file-and-start-a-job"></a>A JAR-fájl feltöltése és a egy feladat indítása
Számos módon feltölthet egy fájlt a HDInsight-fürthöz leírtak szerint [Upload data for HDInsight az Apache Hadoop-feladatok](hdinsight-upload-data.md). Az alábbi lépéseket az Azure PowerShell-lel.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Miután telepítése és konfigurálása az Azure PowerShell-lel, hozzon létre egy új fájlt **hbase-runner.psm1**. Ez a fájl tartalmát a következő használja:

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

    Ez a fájl két modult tartalmaz:

   * **Adjon hozzá HDInsightFile** – fájlok feltöltése HDInsight használatos
   * **Start-HBaseExample** – az a korábban létrehozott osztályok futtatása
2. Mentse a **hbase-runner.psm1** fájlt.
3. Nyisson meg egy új Azure PowerShell-ablakot, lépjen a **hbaseapp** könyvtárat, és futtassa a következő parancsot.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Módosítsa az elérési helyét az **hbase-runner.psm1** korábban létrehozott fájlt. Ez a modul az Azure PowerShell-munkamenetet regisztrál.
4. A következő paranccsal töltse fel a **hbaseapp – 1.0-SNAPSHOT.jar** a HDInsight-fürthöz.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Cserélje le **hdinsightclustername** a HDInsight-fürt nevére. A parancs feltölti az **hbaseapp – 1.0-SNAPSHOT.jar** , a **példa/JAR-fájlok kivételével** a HDInsight-fürthöz az elsődleges tárolási helyét.
5. A fájlok feltöltése után a következő kód használatával létrehozhat egy táblát a a **hbaseapp**:

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Cserélje le **hdinsightclustername** a HDInsight-fürt nevére.

    Ez a parancs létrehoz egy új táblát nevű **személyek** a HDInsight-fürtben. Ez a parancs nem jeleníti meg a kimenetet a konzolablakban.
6. Keresse meg a táblázat, használja a következő parancsot:

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Cserélje le **hdinsightclustername** a HDInsight-fürt nevére.

    Ez a parancs a **SearchByEmail** osztály keresse meg azokat a sorokat, ahol a **contactinformation** oszlopcsalád és a **e-mail** oszlop tartalmazza a karakterláncot **contoso.com**. A következő eredményeket kell kapnia:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Használatával **fabrikam.com** számára a `-emailRegex` értéket adja vissza a felhasználóknak, akiknek **fabrikam.com** az e-mailek mezőben. A keresés reguláris kifejezésen alapuló szűrő használatával valósul meg, mivel is megadhat reguláris kifejezéseket, például **^ r**, mely értéket ad vissza bejegyzéseket, ahol az e-mailben "r" betűvel kezdődik.

## <a name="delete-the-table"></a>A tábla törlése
Amikor elkészült, a példában, használja az Azure PowerShell-munkamenet következő parancsát törli a **személyek** ebben a példában használt táblák:

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Cserélje le **hdinsightclustername** a HDInsight-fürt nevére.

## <a name="troubleshooting"></a>Hibaelhárítás
### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Nincsenek eredmények vagy a Start-HBaseExample használatakor nem várt eredmények
Használja a `-showErr` paraméter megtekintéséhez a normál hiba (STDERR), amely a feladat futtatásakor jön létre.
