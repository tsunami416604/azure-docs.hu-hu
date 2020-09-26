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
ms.openlocfilehash: e4a52095f7fc1c8252ffbbbbedf4520773d3efc1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377044"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Az operációs rendszerhez tartozó legújabb [.net Core ügyféloldali kódtár](https://dotnet.microsoft.com/download/dotnet-core) .
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../create-communication-resource.md).

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `UserAccessTokensQuickstart` . Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: **program.cs**.

```console
dotnet new console -o UserAccessTokensQuickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába, és használja az `dotnet build` parancsot az alkalmazás fordításához.

```console
cd UserAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>A csomag telepítése

Miközben még mindig az alkalmazás könyvtárában van, telepítse a .NET-csomaghoz készült Azure Communication Services Felügyeleti függvénytárat a `dotnet add package` paranccsal.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.1
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. **Program.cs** -fájl megnyitása egy szövegszerkesztőben
1. `using`A névteret tartalmazó direktíva `Azure.Communication.Administration` hozzáadása
1. A `Main` metódus deklarációjának frissítése az aszinkron kód támogatásához

A kezdéshez használja a következő kódot:

```csharp
using System;
using Azure.Communication.Administration;

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Inicializáljon egy- `CommunicationIdentityClient` t a saját kapcsolatok karakterláncával. Az alábbi kód egy nevű környezeti változóból kérdezi le az erőforráshoz tartozó kapcsolatok karakterláncát `COMMUNICATION_SERVICES_CONNECTION_STRING` . Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../create-communication-resource.md#store-your-connection-string).

Adja hozzá a következő kódot a `Main` metódushoz:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>Felhasználó létrehozása

Az Azure kommunikációs szolgáltatások egy egyszerűsített identitási könyvtárat tartanak fenn. A `createUser` metódus használatával hozzon létre egy új bejegyzést a címtárban egyedi értékkel `Id` . Az alkalmazás felhasználóinak és a kommunikációs szolgáltatások által generált identitások (például az alkalmazás-kiszolgáló adatbázisában való tárolás) közötti leképezést kell fenntartani.

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>Felhasználói hozzáférési tokenek kiadása

A `issueToken` metódus használatával kiállíthatja a kommunikációs szolgáltatások felhasználójának hozzáférési jogkivonatát. Ha nem adja meg a választható `user` paramétert, a rendszer új felhasználót hoz létre, és visszaadja a tokent.

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

A felhasználói hozzáférési tokenek olyan rövid élettartamú hitelesítő adatok, amelyeket újra kell adni ahhoz, hogy a felhasználók megakadályozzák a szolgáltatás megszakadását. A `expiresOn` Response tulajdonság a jogkivonat élettartamát jelzi.

## <a name="revoke-user-access-tokens"></a>Felhasználói hozzáférési tokenek visszavonása

Bizonyos esetekben előfordulhat, hogy explicit módon vissza kell vonnia a felhasználói hozzáférési jogkivonatokat, például amikor egy felhasználó módosítja a szolgáltatásban való hitelesítéshez használt jelszót. Ez a funkció az Azure kommunikációs szolgáltatások felügyeleti ügyféloldali könyvtárán keresztül érhető el.

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>Felhasználó törlése

Az identitások törlése visszavonja az összes aktív jogkivonatot, és megakadályozza, hogy az identitások számára további jogkivonatokat bocsásson ki. Emellett eltávolítja a felhasználóhoz társított összes megőrzött tartalmat is.

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```console
dotnet run
```
