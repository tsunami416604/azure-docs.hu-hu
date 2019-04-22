---
title: Csatlakozás az Azure Media Services v3 API – Java
description: Ismerje meg, hogyan csatlakozhat a Media Services v3 API a Javával.
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
ms.date: 04/16/2019
ms.author: juliako
ms.openlocfilehash: 27a4a30dd0eb449726a99d02f2409632aa327567
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683426"
---
# <a name="connect-to-media-services-v3-api---java"></a>Csatlakozás a Media Services v3 API – Java

Ez a cikk bemutatja, hogyan csatlakozhat az Azure Media Services v3 Java SDK használatával a szolgáltatás egyszerű bejelentkezési módszert.

Ez a cikk a Visual Studio Code segítségével fejlesztése a mintaalkalmazást.

## <a name="prerequisites"></a>Előfeltételek

- Hajtsa végre a [írása Java és a Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) telepítéséhez:

   - JDK
   - Az Apache Maven
   - Java Extension Pack
- Feltétlenül állítson be `JAVA_HOME` és `PATH` környezeti változókat.
- [A Media Services-fiók létrehozása](create-account-cli-how-to.md). Ne felejtse el az erőforráscsoport nevét és a Media Services-fiók nevét.
- Kövesse a [hozzáférés API-k](access-api-cli-how-to.md) témakör. Jegyezze fel az előfizetés-azonosító, Alkalmazásazonosítót (ügyfél-azonosító), a hitelesítési kulcs (titkos) és a bérlő Azonosítóját kell egy későbbi lépésben.

Emellett tekintse át:

- [A Visual Studio Code-ban a Java](https://code.visualstudio.com/docs/languages/java)
- [A VS Code-ban a Java projektvezetés](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>Hozzon létre egy Maven-projektet

Nyisson meg egy parancssori eszköz, és `cd` ahhoz a könyvtárhoz, ahol szeretné a projekt létrehozásához.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

A parancs futtatásakor a `pom.xml`, `App.java`, és egyéb fájlok jönnek létre. 

## <a name="add-dependencies"></a>Függőségek hozzáadása

1. A Visual Studio Code-ban nyissa meg a mappát, ahol a projekthez
1. Keresse meg és nyissa meg a `pom.xml`
1. Adja hozzá a szükséges függőségek

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
      <artifactId>azure-mgmt-media</artifactId>
      <version>1.0.0-beta</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.rest</groupId>
      <artifactId>client-runtime</artifactId>
      <version>1.6.5</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-client-authentication</artifactId>
      <version>1.6.5</version>
    </dependency>
    ```

## <a name="connect-to-the-java-client"></a>A Java-ügyfél csatlakozhat.

1. Nyissa meg a `App.java` fájlt `src\main\java\com\azure\ams` és ellenőrizze, hogy a csomag részét képezi az oldal tetején:

    ```java
    package com.azure.ams;
    ```
1. A csomag utasítás alatt adja hozzá ezeket kimutatások importálása:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Szeretne létrehozni az Active Directorybeli hitelesítő adatokat igénylő kérelmeket, a fő metódus az alkalmazás osztály adja hozzá az alábbi kódot, és állítsa be a portáltól kapott értékeket [hozzáférés API-k](access-api-cli-how-to.md):
   
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

- [A Media Services – alapelvek](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java-referencia](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>További lépések

Most hozzáadhatja a `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` és kezelésére szolgáló entitásokat.
