---
title: 'Végfelhasználói hitelesítés: Azure Active Directory használatával az Azure Data Lake Storage Gen1 Java |} A Microsoft Docs'
description: Ismerje meg, hogyan érhet el a végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 a Java használatával Azure Active Directory használatával
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 47b975b3ea0cfa9d2fb2536236b0a8dfaef14503
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126938"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Végfelhasználói hitelesítés az Azure Data Lake Storage Gen1 a Java használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

Ebben a cikkben megismerkedhet a Java SDK használata az Azure Data Lake Storage Gen1 végfelhasználói hitelesítés végrehajtásához. Szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 Java SDK-t, lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 Java használatával](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre a "Natív" Azure Active Directory-alkalmazás**. El kell végeznie a lépések [végfelhasználói hitelesítés a Data Lake Storage Gen1 az Azure Active Directoryval](data-lake-store-end-user-authenticate-using-active-directory.md).

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
   
    Az első függőség az, hogy a Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`) a maven tárházból. A második függőség az alkalmazással használandó naplózási keretrendszer (`slf4j-nop`) meghatározása. Használja a Data Lake Storage Gen1 SDK [slf4j](http://www.slf4j.org/) naplózási előtérrendszer, amellyel számos elterjedt naplózási keretrendszer például log4j, Java-naplózás, logback, stb., választhat, vagy nincs naplózás. Ebben a példában kikapcsoljuk a naplózást, mivel az **slf4j-nop** kötést eszközt használjuk. Az alkalmazásban való egyéb naplózási lehetőségek használatáról [itt talál információt](http://www.slf4j.org/manual.html#projectDep).

3. Adja hozzá az alábbi importálási utasításokat az alkalmazáshoz.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. A Java-alkalmazás az alábbi kódrészlet használatával szerezheti be a korábban létrehozott Active Directory natív alkalmazás jogkivonatát az `DeviceCodeTokenProvider`. Cserélje le **FILL-in-HERE** az Azure Active Directory natív alkalmazás tényleges értéke alapján.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

A Data Lake Storage Gen1 SDK kényelmes módszert is, amelyekkel kezelheti a Data Lake Storage Gen1 fiók kommunikáljon szükséges biztonsági jogkivonatokat biztosít. Azonban az SDK nem írja elő, hogy kizárólag ezek a módszerek használhatók. A jogkivonat beszerzésére más eszközöket is használhat, például az [Active Directory SDK-t](https://github.com/AzureAD/azure-activedirectory-library-for-java) , vagy saját egyéni kódot.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan végfelhasználói hitelesítés használata az Azure Data Lake Storage Gen1 hitelesítést, a Java SDK használatával. Most már megtekintheti a következő cikkeket, hogy hogyan használhatja a Java SDK használata Azure Data Lake Storage Gen1 beszélni.

* [A Data Lake Storage Gen1 Adatműveletek Java SDK használatával](data-lake-store-get-started-java-sdk.md)


