---
title: "Végfelhasználói hitelesítés: az Azure Active Directory használatával a Data Lake Store Java |} Microsoft Docs"
description: "Megtudhatja, hogyan végfelhasználói hitelesítési elérése a Data Lake Store Java az Azure Active Directory használatával"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: b1fbf3c1e0e5715ff7effcdac45ff03f2b55f9ee
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-java"></a>A Data Lake Store Java használatával végfelhasználói hitelesítés
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

Ebből a cikkből megismerheti a végfelhasználói hitelesítést az Azure Data Lake Store Java SDK használatával kapcsolatos. A szolgáltatások közötti hitelesítéshez a Data Lake Store Java SDK használatával, lásd: [szolgáltatások közötti hitelesítés a Data Lake Store Java használatával](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy "Natív" Azure Active Directory-alkalmazás**. Végrehajtotta a lépéseket [végfelhasználói hitelesítés az Azure Active Directory használatával a Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Ez az oktatóanyag a Mavent használja a build- és projektfüggőségek kezeléséhez. Bár lehetséges olyan rendszerek nélkül fejleszteni, mint például a Maven vagy a Gradle, ezekkel a fejlesztőrendszerekkel sokkal egyszerűbb a függőségek kezelése.

* (Nem kötelező) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) vagy hasonló integrált fejlesztőkörnyezet.

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
1. Hozzon létre egy Maven-projektet az [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) használatával parancssorból vagy egy IDE használatával. A Java-projektek IntelliJ használatával való létrehozási útmutatójáért [kattintson ide](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). A projektek Eclipse használatával való létrehozási útmutatójáért [kattintson ide](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Illessze be a következő függőségeket a Maven **pom.xml** nevű fájljába. Illessze be a következő szövegrészletet a **\</project>** címke elé:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
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
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. A következő kódrészletet használja a Java-alkalmazást a korábban létrehozott Active Directory natív alkalmazás jogkivonat beszerzése az `DeviceCodeTokenProvider`. Cserélje le **FILL Itt** az Azure Active Directory natív alkalmazás tényleges értékeket.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

A Data Lake Store SDK kényelmes megoldásaival kezelheti a Data Lake Store-fiókkal való kommunikációhoz szükséges biztonsági jogkivonatokat. Azonban az SDK nem írja elő, hogy kizárólag ezek a módszerek használhatók. A jogkivonat beszerzésére más eszközöket is használhat, például az [Active Directory SDK-t](https://github.com/AzureAD/azure-activedirectory-library-for-java) , vagy saját egyéni kódot.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta végfelhasználói hitelesítés használata a hitelesítéshez az Azure Data Lake Store Java SDK használatával. Most már megtekintheti a következő cikkekben talál arról, hogyan működik az Azure Data Lake Store Java SDK használatával kommunikálhatnak.

* [Adatok műveletek a Data Lake Store Java SDK használatával](data-lake-store-get-started-java-sdk.md)


