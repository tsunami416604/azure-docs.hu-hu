---
title: 'Szolgáltatások közötti hitelesítés: Java az Azure Data Lake Storage Gen1 és az Azure Active Directory |} A Microsoft Docs'
description: Ismerje meg, hogyan érhet el a szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 Java az Azure Active Directory használatával
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c32eada2acca73e089c2296ce8e59c529d7af665
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879165"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 Java használatával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

Ebben a cikkben megismerkedhet a Java SDK használata a szolgáltatások közötti hitelesítés az Azure Data Lake Storage Gen1 tennie. Végfelhasználói hitelesítés a Data Lake Storage Gen1 Java SDK használata nem támogatott.

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Az Azure Active Directory "" webalkalmazás létrehozásához**. El kell végeznie a lépések [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 az Azure Active Directoryval](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Ez az oktatóanyag a Mavent használja a build- és projektfüggőségek kezeléséhez. Bár lehetséges olyan rendszerek nélkül fejleszteni, mint például a Maven vagy a Gradle, ezekkel a fejlesztőrendszerekkel sokkal egyszerűbb a függőségek kezelése.

* (Nem kötelező) Az integrált fejlesztőkörnyezet [az IntelliJ IDEA](https://www.jetbrains.com/idea/download/) vagy [Eclipse](https://www.eclipse.org/downloads/) vagy hasonló.

## <a name="service-to-service-authentication"></a>Szolgáltatások közötti hitelesítés
1. Hozzon létre egy Maven-projektet az [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) használatával parancssorból vagy egy IDE használatával. A Java-projektek IntelliJ használatával való létrehozási útmutatójáért [kattintson ide](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). A projektek Eclipse használatával való létrehozási útmutatójáért [kattintson ide](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

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
   
    Az első függőség az, hogy a Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`) a maven tárházból. A második függőség az alkalmazással használandó naplózási keretrendszer (`slf4j-nop`) meghatározása. Használja a Data Lake Storage Gen1 SDK [slf4j](https://www.slf4j.org/) naplózási előtérrendszer, amellyel számos elterjedt naplózási keretrendszer például log4j, Java-naplózás, logback, stb., választhat, vagy nincs naplózás. Ebben a példában kikapcsoljuk a naplózást, mivel az **slf4j-nop** kötést eszközt használjuk. Az alkalmazásban való egyéb naplózási lehetőségek használatáról [itt talál információt](https://www.slf4j.org/manual.html#projectDep).

3. Adja hozzá az alábbi importálási utasításokat az alkalmazáshoz.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. A következő kódrészletet használja a Java-alkalmazás az Active Directory-webalkalmazás, korábban létrehozott egyik alosztályának jogkivonat beszerzése `AccessTokenProvider` (az alábbi példában `ClientCredsTokenProvider`). A jogkivonat-szolgáltató a memóriában gyorsítótárazza a jogkivonat beszerzéséhez használt hitelesítő adatokat, és automatikusan megújítja azt, ha közeleg a lejárati ideje. Hozzon létre saját alosztályának lehet `AccessTokenProvider` így jogkivonatok lekérésének által a felhasználói kód. Most használjuk az SDK-ban megadott.

    Cserélje ki a **FILL-IN-HERE** értéket az Azure Active Directory-webalkalmazáshoz tartozó tényleges értékkel.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

A Data Lake Storage Gen1 SDK kényelmes módszert is, amelyekkel kezelheti a Data Lake Storage Gen1 fiók kommunikáljon szükséges biztonsági jogkivonatokat biztosít. Azonban az SDK nem írja elő, hogy kizárólag ezek a módszerek használhatók. A jogkivonat beszerzésére más eszközöket is használhat, például az [Active Directory SDK-t](https://github.com/AzureAD/azure-activedirectory-library-for-java) , vagy saját egyéni kódot.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan végfelhasználói hitelesítés használatára való hitelesítés a Data Lake Storage Gen1 Java SDK-t. Most már megtekintheti a következő cikkek, amelyek a Java SDK használata dolgozhat a Data Lake Storage Gen1 beszélni.

* [A Data Lake Storage Gen1 Adatműveletek Java SDK használatával](data-lake-store-get-started-java-sdk.md)


