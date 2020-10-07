---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: a9c8d604e5564526936f37edcc9eec5891443a47
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779738"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) 8-as vagy újabb verziója.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Egy üzembe helyezett kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../create-communication-resource.md).

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-java-application"></a>Új Java-alkalmazás létrehozása

Nyissa meg a terminál vagy a parancssorablakot, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Java-alkalmazást. Futtassa az alábbi parancsot a Java-projekt létrehozásához a Maven-archetípus-Gyorsindítás sablonból.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Megfigyelheti, hogy a "létrehozás" feladat létrehozta a könyvtárat ugyanazzal a névvel `artifactId` . Ebben a könyvtárban a src/Main/Java könyvtár tartalmazza a projekt forráskódját, a `src/test/java directory` tartalmazza a teszt forrását, a `pom.xml` fájl pedig a projekt projekt-objektummodell-modellje, vagy Pom.

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a **pom.xml** fájlt a szövegszerkesztőben. Adja hozzá a függőségek csoportjához a következő függőségi elemet.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Navigáljon a */src/Main/Java/com/Communication/Quickstart* könyvtárához
1. Nyissa meg az *app. Java* fájlt a szerkesztőben
1. Az `System.out.println("Hello world!");` utasítás cseréje
1. `import`Irányelvek hozzáadása

A kezdéshez használja a következő kódot:

```java
import com.azure.communication.common.CommunicationUser;
import com.azure.communication.administration.models.CommunicationIdentityToken;
import com.azure.communication.administration.CommunicationIdentityClient;
import com.azure.communication.administration.CommunicationIdentityClientBuilder;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - User Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Példány létrehozása az `CommunicationIdentityClient` erőforráshoz tartozó hozzáférési kulccsal és végponttal. Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../create-communication-resource.md#store-your-connection-string).

Adja hozzá a következő kódot a `main` metódushoz:

```java
// Your can find your endpoint and access token from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessToken = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessToken))
    .httpClient(httpClient)
    .buildClient();
```

Az ügyfelet bármely olyan egyéni HTTP-ügyféllel inicializálhatja, amely megvalósítja a `com.azure.core.http.HttpClient` felületet. A fenti kód azt mutatja be, hogy az [Azure alapszintű](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true) , az által biztosított http-ügyfelet használja `azure-core` .

## <a name="create-a-user"></a>Felhasználó létrehozása

Az Azure kommunikációs szolgáltatások egy egyszerűsített identitási könyvtárat tartanak fenn. A `createUser` metódus használatával hozzon létre egy új bejegyzést a címtárban egyedi értékkel `Id` . Az alkalmazás felhasználóinak és a kommunikációs szolgáltatások által generált identitások (például az alkalmazás-kiszolgáló adatbázisában való tárolás) közötti leképezést kell fenntartani.

```java
CommunicationUser user = communicationIdentityClient.createUser();
System.out.println("\nCreated a user with ID: " + user.getId());
```

## <a name="issue-user-access-tokens"></a>Felhasználói hozzáférési tokenek kiadása

A `issueToken` metódus használatával kiállíthatja a kommunikációs szolgáltatások felhasználójának hozzáférési jogkivonatát. Ha nem adja meg a választható `user` paramétert, a rendszer új felhasználót hoz létre, és visszaadja a tokent.

```java
// Issue an access token with the "voip" scope for a new user
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(user, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String userId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + userId + ": " + token);
System.out.println(token);
```

A felhasználói hozzáférési tokenek olyan rövid élettartamú hitelesítő adatok, amelyeket újra kell adni ahhoz, hogy a felhasználók megakadályozzák a szolgáltatás megszakadását. A `expiresAt` Response tulajdonság a jogkivonat élettartamát jelzi.

## <a name="revoke-user-access-tokens"></a>Felhasználói hozzáférési tokenek visszavonása

Bizonyos esetekben előfordulhat, hogy explicit módon vissza kell vonnia a felhasználói hozzáférési jogkivonatokat, például amikor egy felhasználó módosítja a szolgáltatásban való hitelesítéshez használt jelszót. Ezzel a `revokeTokens` módszerrel érvénytelenítheti az összes felhasználó hozzáférési jogkivonatát.

```java  
communicationIdentityClient.revokeTokens(user, OffsetDateTime.now());
System.out.println("\nRevoked tokens for the user with ID: " + user.getId());
```

## <a name="delete-a-user"></a>Felhasználó törlése

Egy felhasználó törlése visszavonja az összes aktív jogkivonatot, és megakadályozza, hogy az identitások számára további jogkivonatokat bocsásson ki. Emellett eltávolítja a felhasználóhoz társított összes megőrzött tartalmat is.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nSuccessfully deleted the identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>A kód futtatása

Navigáljon a *pom.xml* fájlt tartalmazó könyvtárra, és fordítsa le a projektet a következő `mvn` parancs használatával.

```console
mvn compile
```

Ezután hozza létre a csomagot.

```console
mvn package
```

Futtassa az alábbi `mvn` parancsot az alkalmazás végrehajtásához.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
