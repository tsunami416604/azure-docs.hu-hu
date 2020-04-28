---
title: 'Végfelhasználói hitelesítés: Java a Azure Data Lake Storage Gen1 használatával Azure Active Directory | Microsoft Docs'
description: Ismerje meg, hogyan érheti el a végfelhasználói hitelesítést a Azure Data Lake Storage Gen1 használatával a Azure Active Directory Javával
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
ms.openlocfilehash: 8b558fca964f33d47d331e007329d1bae2626877
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "60878101"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Végfelhasználói hitelesítés Azure Data Lake Storage Gen1 a Javával
> [!div class="op_single_selector"]
> * [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
> * [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
> * [A Python használata](data-lake-store-end-user-authenticate-python.md)
> * [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

Ebből a cikkből megtudhatja, hogyan használhatja a Java SDK-t a végfelhasználói hitelesítésre Azure Data Lake Storage Gen1 használatával. A szolgáltatások közötti hitelesítéshez a Java SDK-val Data Lake Storage Gen1 használatával lásd: [szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 a Java használatával](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

* **Hozzon létre egy Azure Active Directory "natív" alkalmazást**. A Data Lake Storage Gen1 a Azure Active Directory használatával végre kell hajtania a [végfelhasználói hitelesítéshez](data-lake-store-end-user-authenticate-using-active-directory.md)szükséges lépéseket.

* [Maven](https://maven.apache.org/install.html). Ez az oktatóanyag a Mavent használja a build- és projektfüggőségek kezeléséhez. Bár lehetséges olyan rendszerek nélkül fejleszteni, mint például a Maven vagy a Gradle, ezekkel a fejlesztőrendszerekkel sokkal egyszerűbb a függőségek kezelése.

* (Nem kötelező) [IntelliJ IDEA](https://www.jetbrains.com/idea/download/), [Eclipse](https://www.eclipse.org/downloads/) vagy hasonló integrált fejlesztőkörnyezet.

## <a name="end-user-authentication"></a>Végfelhasználói hitelesítés
1. Hozzon létre egy Maven-projektet az [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) használatával parancssorból vagy egy IDE használatával. A Java-projektek IntelliJ használatával való létrehozási útmutatójáért [kattintson ide](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). A projektek Eclipse használatával való létrehozási útmutatójáért [kattintson ide](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Illessze be a következő függőségeket a Maven **pom.xml** nevű fájljába. Adja hozzá a következő kódrészletet a ** \</Project>** címke előtt:
   
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
   
    Az első függőség a Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`) használata a Maven adattárból. A második függőség az alkalmazással használandó naplózási keretrendszer (`slf4j-nop`) meghatározása. A Data Lake Storage Gen1 SDK a [slf4j](https://www.slf4j.org/) -naplózási homlokzatot használja, amely számos népszerű naplózási keretrendszer közül választhat, például Log4j, Java-naplózás, logback stb., vagy nincs naplózás. Ebben a példában kikapcsoljuk a naplózást, mivel az **slf4j-nop** kötést eszközt használjuk. Az alkalmazásban való egyéb naplózási lehetőségek használatáról [itt talál információt](https://www.slf4j.org/manual.html#projectDep).

3. Adja hozzá az alábbi importálási utasításokat az alkalmazáshoz.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. A Java-alkalmazásban az alábbi kódrészlettel szerezheti be a korábban a `DeviceCodeTokenProvider`használatával létrehozott Active Directory natív alkalmazás jogkivonatát. Cserélje le a **Fill-in-here** értéket a Azure Active Directory natív alkalmazás tényleges értékeire.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

A Data Lake Storage Gen1 SDK kényelmes módszereket biztosít a Data Lake Storage Gen1-fiókkal való kommunikációhoz szükséges biztonsági jogkivonatok kezeléséhez. Azonban az SDK nem írja elő, hogy kizárólag ezek a módszerek használhatók. A jogkivonat beszerzésére más eszközöket is használhat, például az [Active Directory SDK-t](https://github.com/AzureAD/azure-activedirectory-library-for-java) , vagy saját egyéni kódot.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan használható a végfelhasználói hitelesítés a Azure Data Lake Storage Gen1 a Java SDK-val való hitelesítéshez. A következő cikkekből megtudhatja, hogyan használhatja a Java SDK-t a Azure Data Lake Storage Gen1-vel való együttműködésre.

* [Az adatműveletek Data Lake Storage Gen1 a Java SDK használatával](data-lake-store-get-started-java-sdk.md)


