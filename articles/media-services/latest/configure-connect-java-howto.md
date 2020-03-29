---
title: Csatlakozás az Azure Media Services v3 API-hoz – Java
description: Ez a cikk ismerteti, hogyan csatlakozhat az Azure Media Services v3 API-jához a Java.This article describes how to connect to Azure Media Services v3 API with Java.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: 6b0f21c3fa7a9c827f7201f4b899a33ea77eaf08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888495"
---
# <a name="connect-to-media-services-v3-api---java"></a>Csatlakozás a Media Services v3 API-hoz – Java

Ez a cikk bemutatja, hogyan csatlakozhat az Azure Media Services v3 Java SDK-hoz az egyszerű szolgáltatás bejelentkezési módszer használatával.

Ebben a cikkben a Visual Studio-kód a mintaalkalmazás fejlesztéséhez használatos.

## <a name="prerequisites"></a>Előfeltételek

- A telepítéshez kövesse [a Java írása a Visual Studio-kóddal című témakört:](https://code.visualstudio.com/docs/java/java-tutorial)

   - JDK
   - Apache Maven
   - Java bővítménycsomag
- Győződjön meg `JAVA_HOME` `PATH` róla, hogy állítsa be, és a környezeti változók.
- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md). Ne felejtse el megjegyezni az erőforráscsoport nevét és a Media Services-fiók nevét.
- Kövesse az [Access API-k témakörlépéseit.](access-api-cli-how-to.md) Rögzítse az előfizetés-azonosítót, az alkalmazásazonosítót (ügyfélazonosító), a hitelesítési kulcsot (titkos) és a bérlői azonosítót, amelyegy későbbi lépésben szükséges.

Szintén felülvizsgálat:

- [Java a Visual Studio-kódban](https://code.visualstudio.com/docs/languages/java)
- [Java projektmenedzsment vs kódban](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Tekintse át [az elnevezési konvenciókat.](media-services-apis-overview.md#naming-conventions)

## <a name="create-a-maven-project"></a>Maven-projekt létrehozása

Nyisson meg egy `cd` parancssori eszközt, és egy könyvtárba, ahová a projektet létre szeretné hozni.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

A parancs futtatásakor `pom.xml` `App.java`létrejön a , a és más fájlok. 

## <a name="add-dependencies"></a>Függőségek hozzáadása

1. A Visual Studio-kódban nyissa meg azt a mappát, amelyben a projekt
1. Keresse meg és nyissa meg a`pom.xml`
1. A szükséges függőségek hozzáadása

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Csatlakozás a Java-ügyfélhez

1. Nyissa `App.java` meg `src\main\java\com\azure\ams` a fájl alatt, és győződjön meg arról, hogy a csomag szerepel a tetején:

    ```java
    package com.azure.ams;
    ```
1. A csomagutasítás alatt adja hozzá a következő importálási nyilatkozatokat:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. A kérelmekhez szükséges Active Directory-hitelesítő adatok létrehozásához adja hozzá a következő kódot az alkalmazásosztály fő metódusához, és állítsa be az [Access API-kból](access-api-cli-how-to.md)kapott értékeket:
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Futtassa az alkalmazást.

## <a name="see-also"></a>Lásd még

- [A Media Services fogalmai](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java-hivatkozás](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>További lépések

Most már `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` felveheti és elkezdheti az entitások manipulálását.

További kódpéldákért tekintse meg a [Java SDK-minták](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) tártárát.
