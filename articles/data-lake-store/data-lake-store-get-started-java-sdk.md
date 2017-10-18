---
title: "Java SDK: Fájlrendszerműveletek az Azure Data Lake Store-ban | Microsoft Docs"
description: "Az Azure Data Lake Store Java SDK használata fájlrendszerműveletek (például mappák létrehozása) végrehajtására a Data Lake Store-ban"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/29/2017
ms.author: nitinme
ms.openlocfilehash: e8c7b788061b3eb18b3e6c282339a03d93ab8b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="filesystem-operations-on-data-lake-store-using-java-sdk"></a>Fájlrendszerműveletek a Data Lake Store-on a Java SDK használatával
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

A cikkből megtudhatja, hogyan végezhet el olyan alapvető műveleteket az Azure Data Lake Store Java SDK segítségével, mint például mappák létrehozása vagy adatfájlok le- és feltöltése. További információk a Data Lake-ről: [Azure Data Lake Store](data-lake-store-overview.md).

Az Azure Data Lake Store Java SDK API-dokumentációja [Az Azure Data Lake Store Java API-dokumentációja](https://azure.github.io/azure-data-lake-store-java/javadoc/) című témakörben található.

## <a name="prerequisites"></a>Előfeltételek
* Java-fejlesztőkészlet (JDK 7 vagy újabb, Java 1.7 vagy újabb verzió használatával)
* Azure Data Lake Store-fiók. Kövesse [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](data-lake-store-get-started-portal.md) című témakör utasításait.
* [Maven](https://maven.apache.org/install.html). Ez az oktatóanyag a Mavent használja a build- és projektfüggőségek kezeléséhez. Bár lehetséges olyan rendszerek nélkül fejleszteni, mint például a Maven vagy a Gradle, ezekkel a fejlesztőrendszerekkel sokkal egyszerűbb a függőségek kezelése.
* (Nem kötelező) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) vagy hasonló integrált fejlesztőkörnyezet.

## <a name="create-a-java-application"></a>Java-alkalmazás létrehozása
A [GitHubon](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) elérhető kódminta végigvezeti a fájlok tárolóban való létrehozásának, a fájlok összetűzésének, a fájlok letöltésének és az egyes fájlok tárolóból való törlésének folyamatán. A cikk ezen szakasza a kód fő részeit mutatja be.

1. Hozzon létre egy Maven-projektet az [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) használatával parancssorból vagy egy IDE használatával. A Java-projektek IntelliJ használatával való létrehozási útmutatójáért [kattintson ide](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). A projektek Eclipse használatával való létrehozási útmutatójáért [kattintson ide](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Illessze be a következő függőségeket a Maven **pom.xml** nevű fájljába. Illessze be a következő szövegrészletet a **\</project>** címke elé:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    Az első függőség a Data Lake Store SDK (`azure-data-lake-store-sdk`) használata a Maven tárházból. A második függőség az alkalmazással használandó naplózási keretrendszer (`slf4j-nop`) meghatározása. A Data Lake Store SDK az [slf4j](http://www.slf4j.org/) naplózási megoldást használja, amellyel számos elterjedt naplózási keretrendszer közül választhat (például log4j, Java-naplózás, Logback vagy nincs naplózás). Ebben a példában kikapcsoljuk a naplózást, mivel az **slf4j-nop** kötést eszközt használjuk. Az alkalmazásban való egyéb naplózási lehetőségek használatáról [itt talál információt](http://www.slf4j.org/manual.html#projectDep).

3. Adja hozzá az alábbi importálási utasításokat az alkalmazáshoz.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Authentication

* Az alkalmazás végfelhasználói hitelesítésével kapcsolatban lásd: [Végfelhasználói hitelesítés a Data Lake Store-ban a Java használatával](data-lake-store-end-user-authenticate-java-sdk.md).
* Az alkalmazás szolgáltatások közötti hitelesítésével kapcsolatban lásd: [Szolgáltatások közötti hitelesítés a Data Lake Store-ban a Java használatával](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-an-azure-data-lake-store-client"></a>Azure Data Lake Store-ügyfél létrehozása
Az [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) objektum létrehozásakor meg kell adnia a Data Lake Store-fiók nevét és az előző lépésben létrehozott jogkivonat-szolgáltatót, ha megtörtént a Data Lake Store-hitelesítés (lásd a [Hitelesítés](#authentication) szakaszt). A Data Lake Store-fióknév csak teljes tartománynév lehet. A **FILL-IN-HERE** értéket például a következő tartománynévre cserélheti ki: **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

Az alábbi szakaszokban szereplő kódrészletek néhány gyakori fájlrendszerműveletre mutatnak példát. Az egyéb műveleteket az **ADLStoreClient** objektumhoz tartozó [Data Lake Store Java SDK teljes API-dokumentációjában](https://azure.github.io/azure-data-lake-store-java/javadoc/) tekintheti meg.

## <a name="create-a-directory"></a>Könyvtár létrehozása

Az alábbi kódrészlet könyvtárstruktúrát hoz létre a megadott Data Lake Store-fiók gyökérmappájában.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>Fájl létrehozása

Az alábbi kódrészlet egy fájlt (c.txt) hoz létre a könyvtárstruktúrában, és adatokat ír ebbe a fájlba.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

Fájl (d.txt) létrehozásához bájttömbök is használhatók.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

Az előző kódrészletben használt `getSampleContent` függvény meghatározása a [GitHubon](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) található minta részeként érhető el. 

## <a name="append-to-a-file"></a>Hozzáfűzés fájlhoz

Az alábbi kódrészlet egy meglévő fájlhoz fűzi hozzá a tartalmakat.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

Az előző kódrészletben használt `getSampleContent` függvény meghatározása a [GitHubon](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) található minta részeként érhető el.

## <a name="read-a-file"></a>Fájl beolvasása

Az alábbi kódrészlet adatokat olvas be egy Data Lake Store-fiókban lévő fájlból.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>Fájlok összefűzése

Az alábbi kódrészlet két fájlt fűz össze a Data Lake Store-fiókban. Ha a művelet sikeres, a rendszer az összefűzött fájllal cseréli le a két meglévő fájlt.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Fájl átnevezése

Az alábbi kódrészlet egy fájlt nevez át a Data Lake Store-fiókban.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Fájl metaadatainak lekérése

Az alábbi kódrészlet egy Data Lake Store-fiókban tárolt fájlt metaadatait kérdezi le.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>Fájlengedélyek beállítása

Az alábbi kódrészlet az előző szakaszban létrehozott fájlon állít be engedélyeket.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

Az alábbi kódrészlet egy könyvtár tartalmát listázza ki rekurzív módon.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

Az előző kódrészletben használt `printDirectoryInfo` függvény meghatározása a [GitHubon](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) található minta részeként érhető el.

## <a name="delete-files-and-folders"></a>Fájlok és mappák törlése

Az alábbi kódrészlet rekurzív módon törli a megadott fájlokat és mappákat egy Data Lake Store-fiókból.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Az alkalmazás fordítása és futtatása
1. Az integrált fejlesztőkörnyezetben történő futtatáshoz keresse meg a **Futtatás** gombot, és kattintson rá. A Mavenben történő futtatáshoz használja az [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html) beépülő modult.
2. Parancssorból futtatható, különálló jar-fájlt az összes függőség és a [Maven Assembly Plugin](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html) használatával hozhat létre. A [GitHubon található mintaforráskód](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) pom.xml fájlja egy példát tartalmaz a fenti műveletre.

## <a name="next-steps"></a>Következő lépések
* [A Java SDK JavaDoc-dokumentációjának áttekintése](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)


