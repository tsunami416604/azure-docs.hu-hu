---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 511548da9767544ace2827a6a8b2baa83b1d2971
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2020
ms.locfileid: "96993014"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Az operációs rendszerhez tartozó legújabb [.net Core ügyféloldali kódtár](https://dotnet.microsoft.com/download/dotnet-core) .
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../create-communication-resource.md).

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `AccessTokensQuickstart` . Ez a parancs egy egyszerű "„Helló világ!” alkalmazás" C#-projektet hoz létre egyetlen forrásfájlban: **program.cs**.

```console
dotnet new console -o AccessTokensQuickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába, és használja az `dotnet build` parancsot az alkalmazás fordításához.

```console
cd AccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>A csomag telepítése

Miközben még mindig az alkalmazás könyvtárában van, telepítse a .NET-csomaghoz készült Azure Communication Services Felügyeleti függvénytárat a `dotnet add package` paranccsal.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.3
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. **Program.cs** -fájl megnyitása egy szövegszerkesztőben
1. `using`A névteret tartalmazó direktíva `Azure.Communication.Administration` hozzáadása
1. A `Main` metódus deklarációjának frissítése az aszinkron kód támogatásához

A kezdéshez használja a következő kódot:

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Administration;

namespace AccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```
## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Inicializáljon egy- `CommunicationIdentityClient` t a saját kapcsolatok karakterláncával. Az alábbi kód egy nevű környezeti változóból kérdezi le az erőforráshoz tartozó kapcsolatok karakterláncát `COMMUNICATION_SERVICES_CONNECTION_STRING` . Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../create-communication-resource.md#store-your-connection-string).

Adja hozzá a következő kódot a `Main` metódushoz:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-an-identity"></a>Identitás létrehozása

Az Azure kommunikációs szolgáltatások egy egyszerűsített identitási könyvtárat tartanak fenn. A `createUser` metódus használatával hozzon létre egy új bejegyzést a címtárban egyedi értékkel `Id` . Tárolja a kapott identitást az alkalmazás felhasználóinak való leképezéssel. Például úgy, hogy az alkalmazás-kiszolgáló adatbázisában tárolja őket. Az identitást később kell megadni a hozzáférési tokenek kiküldéséhez.

```csharp
var identityResponse = await client.CreateUserAsync();
var identity = identityResponse.Value;
Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
```

## <a name="issue-identity-access-tokens"></a>Identitás-hozzáférési tokenek kiadása

A `issueToken` metódus használatával kiállíthat egy hozzáférési jogkivonatot a már meglévő kommunikációs szolgáltatások identitásához. A paraméter olyan `scopes` primitívek készletét határozza meg, amelyek engedélyezik ezt a hozzáférési jogkivonatot. Tekintse meg a [támogatott műveletek listáját](../../concepts/authentication.md). A paraméter új példánya az `communicationUser` Azure kommunikációs szolgáltatás identitásának karakterlánc-ábrázolása alapján hozható létre.

```csharp
// Issue an access token with the "voip" scope for an identity
var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued an access token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

A hozzáférési jogkivonatok olyan rövid élettartamú hitelesítő adatok, amelyeket újra kell adni. Ha ezt nem teszi meg, az alkalmazás felhasználói élményének megszakadását okozhatja. A `expiresOn` Response tulajdonság a hozzáférési jogkivonat élettartamát jelzi. 

## <a name="refresh-access-tokens"></a>Hozzáférési jogkivonatok frissítése

Egy hozzáférési jogkivonat frissítéséhez át kell adni az objektum egy példányát a következőre: `CommunicationUser` `IssueTokenAsync` . Ha ezt a tárolót tárolja `Id` , és létre kell hoznia egy újat, a következő módon elvégezheti a `CommunicationUser` tárolását `Id` a `CommunicationUser` konstruktorba:

```csharp  
// In this example, userId is a string containing the Id property of a previously-created CommunicationUser
identityToRefresh = new CommunicationUser(userId);
tokenResponse = await client.IssueTokenAsync(identityToRefresh, scopes: new [] { CommunicationTokenScope.VoIP });
```

## <a name="revoke-access-tokens"></a>Hozzáférési tokenek visszavonása

Bizonyos esetekben explicit módon visszavonhatja a hozzáférési jogkivonatokat. Például amikor egy alkalmazás felhasználója megváltoztatja a szolgáltatásban való hitelesítéshez használt jelszót. `RevokeTokensAsync`A metódus érvényteleníti az összes aktív hozzáférési jogkivonatot, amelyet az identitáshoz adtak ki.

```csharp  
await client.RevokeTokensAsync(identity);
Console.WriteLine($"\nSuccessfully revoked all access tokens for identity with ID: {identity.Id}");
```

## <a name="delete-an-identity"></a>Identitás törlése

Az identitás törlése visszavonja az összes aktív hozzáférési jogkivonatot, és megakadályozza, hogy hozzáférési jogkivonatokat bocsásson ki az identitásokhoz. Emellett eltávolítja az identitáshoz társított összes megőrzött tartalmat is.

```csharp
await client.DeleteUserAsync(identity);
Console.WriteLine($"\nDeleted the identity with ID: {identity.Id}");
```

## <a name="run-the-code"></a>A kód futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```console
dotnet run
```
