---
title: Szolgáltatás-szolgáltatás hitelesítése - Data Lake Storage Gen1 – Java SDK
description: Megtudhatja, hogy miként érheti el a szolgáltatás-szolgáltatás hitelesítést az Azure Data Lake Storage Gen1 használatával az Azure Active Directory javaval
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f355da7cd9c035b4ed0845bbd374a93bfb4a7350
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904538"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Szolgáltatás-szolgáltatás hitelesítés az Azure Data Lake Storage Gen1 használatával Java használatával

> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
> * [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-service-to-service-authenticate-python.md)
> * [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

Ebben a cikkben megtudhatja, hogyan használhatja a Java SDK szolgáltatás-szolgáltatás hitelesítést az Azure Data Lake Storage Gen1 használatával. A Végfelhasználói hitelesítés a Data Lake Storage Gen1 java SDK használatával nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "web" alkalmazást.** Az Azure Active Directory használatával el kell végeznie a Szolgáltatás-szolgáltatás hitelesítés lépéseit a [Data Lake Storage Gen1 szolgáltatással.](data-lake-store-service-to-service-authenticate-using-active-directory.md)

* [Maven](https://maven.apache.org/install.html). Ez az oktatóanyag a Mavent használja a build- és projektfüggőségek kezeléséhez. Bár a buildrendszer, például a Maven vagy a Gradle használata nélkül is lehetséges, ezek a rendszerek sokkal könnyebbé teszik a függőségek kezelését.

* (Nem kötelező) Egy IDE, mint [az IntelliJ IDEA](https://www.jetbrains.com/idea/download/) vagy [eclipse](https://www.eclipse.org/downloads/) vagy hasonló.

## <a name="service-to-service-authentication"></a>Szolgáltatások közötti hitelesítés

1. Hozzon létre egy Maven-projektet az [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) használatával parancssorból vagy egy IDE használatával. A Java-projektek IntelliJ használatával való létrehozási útmutatójáért [kattintson ide](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). A projektek Eclipse használatával való létrehozási útmutatójáért [kattintson ide](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Illessze be a következő függőségeket a Maven **pom.xml** nevű fájljába. Adja hozzá a következő kódrészletet a ** \</project>** címke elé:

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

    Az első függőség a Data Lake Storage Gen1`azure-data-lake-store-sdk`SDK ( ) használata a maven-tárházból. A második függőség az alkalmazással használandó naplózási keretrendszer (`slf4j-nop`) meghatározása. A Data Lake Storage Gen1 SDK [slf4j](https://www.slf4j.org/) naplózási homlokzatot használ, amely lehetővé teszi számos népszerű naplózási keretrendszer közül a választást, például log4j, Java naplózás, logback stb., vagy nincs naplózás. Ebben a példában kikapcsoljuk a naplózást, mivel az **slf4j-nop** kötést eszközt használjuk. Az alkalmazásban való egyéb naplózási lehetőségek használatáról [itt talál információt](https://www.slf4j.org/manual.html#projectDep).

3. Adja hozzá az alábbi importálási utasításokat az alkalmazáshoz.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. A Java-alkalmazásban az alábbi kódrészlet segítségével szerezze be a korábban létrehozott Active Directory webalkalmazás jogkivonatát `ClientCredsTokenProvider`a (a következő példa használata) egyik alosztályával. `AccessTokenProvider` A jogkivonat-szolgáltató a memóriában gyorsítótárazza a jogkivonat beszerzéséhez használt hitelesítő adatokat, és automatikusan megújítja azt, ha közeleg a lejárati ideje. Lehetőség van arra, hogy saját `AccessTokenProvider` alosztályokat hozzon létre, így a jogkivonatokat az ügyfélkód szerzi be. Most használjuk az SDK-ban megadottat.

    Cserélje ki a **FILL-IN-HERE** értéket az Azure Active Directory-webalkalmazáshoz tartozó tényleges értékkel.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

A Data Lake Storage Gen1 SDK kényelmes módszereket biztosít, amelyek lehetővé teszik a Data Lake Storage Gen1 fiókkal való beszélgetéshez szükséges biztonsági tokenek kezelését. Azonban az SDK nem írja elő, hogy kizárólag ezek a módszerek használhatók. A jogkivonat beszerzésére más eszközöket is használhat, például az [Active Directory SDK-t](https://github.com/AzureAD/azure-activedirectory-library-for-java) , vagy saját egyéni kódot.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan használhatja a végfelhasználói hitelesítést a Data Lake Storage Gen1 java SDK használatával történő hitelesítéshez. Most már tekintse meg a következő cikkeket, amelyek arról beszélnek, hogyan használhatja a Java SDK-t a Data Lake Storage Gen1 használatához.

* [Adatműveletek a Data Lake Storage Gen1 java SDK használatával](data-lake-store-get-started-java-sdk.md)
