---
title: Alkalmazáshitelesítési kód írása
titleSuffix: Azure Digital Twins
description: 'Lásd: hitelesítési kód írása ügyfélalkalmazás'
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: c211c0e5ef0b39f778db7c922fafc735e2411068
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930043"
---
# <a name="write-client-app-authentication-code"></a>Ügyfél-alkalmazás hitelesítési kódjának írása

Miután [beállított egy Azure digitális Twins-példányt és-hitelesítést](how-to-set-up-instance-scripted.md), létrehozhat egy ügyfélalkalmazás, amelyet a példánnyal való interakcióhoz használni fog. Miután beállította a Starter Client projektet, ez a cikk bemutatja, **hogyan írhat kódot az adott ügyfélalkalmazás számára** az Azure Digital Twins-példánnyal való hitelesítéshez.

Ebben a cikkben két módszer létezik a mintakód megírására. A választott nyelvtől függően használhatja az Ön számára legmegfelelőbbet:
* A mintakód első szakasza az Azure Digital Twins .NET (C#) SDK-t használja. Az SDK a .NET-hez készült Azure SDK része, és itt található: az [*Azure IoT Digital Twin ügyféloldali kódtár a .net-hez*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).
* A mintakód második szakasza olyan felhasználók számára készült, akik nem a .NET SDK-t használják, hanem az autorest által generált SDK-kat használják más nyelveken. A stratégiával kapcsolatos további információkért lásd [*: útmutató: egyéni SDK-k létrehozása az Azure Digital Twins szolgáltatáshoz autorest használatával*](how-to-create-custom-sdks.md).

További információ az Azure Digital Twins API-jai és SDK [*-król: az Azure Digital Twins API-k és SDK*](how-to-use-apis-sdks.md)-k használata.

## <a name="prerequisites"></a>Előfeltételek

Először végezze el a telepítési lépéseket a következő [*útmutatóban: példány és hitelesítés beállítása*](how-to-set-up-instance-scripted.md). Ez biztosítja, hogy rendelkezik egy Azure digitális Twins-példánnyal, a felhasználó rendelkezik hozzáférési engedélyekkel, és beállította az ügyfélalkalmazások engedélyeit. Az összes beállítás után készen áll az ügyfélalkalmazás kódjának írására.

A folytatáshoz szüksége lesz egy ügyfélalkalmazás-projektre, amelyben a kódot írja. Ha még nem rendelkezik beállított ügyfélalkalmazás-projekttel, hozzon létre egy alapszintű projektet a választott nyelven az oktatóanyag használatával.

## <a name="authentication-and-client-creation-net-c-sdk"></a>Hitelesítés és ügyfél-létrehozás: .NET (C#) SDK

Először is vegye fel a következő csomagokat a projektbe a .NET SDK és a hitelesítési eszközök használatához ehhez a útmutatóhoz:
* `Azure.DigitalTwins.Core` (verzió `1.0.0-preview.2` )
* `Azure.Identity`

A választott eszközöktől függően a Visual Studio Package Managerrel vagy a parancssori eszközzel is megadhatja a csomagokat `dotnet` . 

A következő utasításokra is szüksége lesz:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```
A .NET SDK-val történő hitelesítéshez használja az [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) könyvtárban definiált hitelesítő adatok beszerzésére szolgáló metódusok egyikét. Az alábbi két módszer általában használatban van (még ugyanabban az alkalmazásban együtt):

* A [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) interaktív alkalmazásokhoz készült, és használható egy hitelesített SDK-ügyfél létrehozásához.
* A [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet) remekül működik olyan esetekben, amikor felügyelt identitásokat (MSI) kell használnia, és jó választás a Azure functions

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential metódus
Az [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) metódus interaktív alkalmazásokhoz készült, és egy webböngészőt hoz létre a hitelesítéshez.

Ha egy hitelesített SDK-ügyfél létrehozásához az interaktív böngésző hitelesítő adatait szeretné használni, adja hozzá a következő kódot:

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Habár az ügyfél-azonosító, a bérlői azonosító és a példány URL-címét közvetlenül a programkódba helyezheti a fentiekben látható módon, érdemes lehet a programkódot egy konfigurációs fájlból vagy környezeti változóból is beolvasni.

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential metódus
 A [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet) metódus nagyban működik olyan esetekben, ahol [felügyelt identitásokra (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)van szükség – például a Azure functions használatakor.
Az Azure-függvényekben a felügyelt identitás hitelesítő adatait használhatja a következőhöz hasonló módon:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

További információ [*: Azure-függvény beállítása az adatok feldolgozásához*](how-to-create-azure-function.md) a függvények kontextusában a fontos konfigurációs beállítások némelyikét bemutató részletesebb példa.

Továbbá a függvények hitelesítésének használatához ne felejtse el a következőket:
* [Felügyelt identitás engedélyezése](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* A megfelelő [környezeti változók](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) használata
* Rendeljen engedélyeket a functions alkalmazáshoz, amely lehetővé teszi, hogy hozzáférjen a digitális Twins API-khoz. Azure Functions folyamatokkal kapcsolatos további információkért lásd: [*útmutató: Azure-függvény beállítása az adatok feldolgozásához*](how-to-create-azure-function.md).

## <a name="authentication-with-an-autorest-generated-sdk"></a>Hitelesítés az autorest által generált SDK-val

Ha nem a .NET-et használja, dönthet úgy, hogy az SDK-függvénytárat tetszőleges nyelven felépíti, ahogy azt a [*útmutató: egyéni SDK-k létrehozása az Azure Digital Twins*](how-to-create-custom-sdks.md)szolgáltatáshoz az autorest használatával című témakör ismerteti.

Ez a szakasz azt ismerteti, hogyan lehet hitelesítést végezni ebben az esetben.

### <a name="prerequisites"></a>Előfeltételek

Először is végre kell hajtania egy egyéni SDK létrehozásához szükséges lépéseket a következő témakörben ismertetett lépéseket követve [*: egyéni SDK-k létrehozása az Azure Digital Twins-hoz az autorest*](how-to-create-custom-sdks.md)használatával.

Ez a példa egy autorest használatával generált írógéppel SDK-t használ. Ennek eredményeképpen az alábbiakra is szükség van:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-Rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Minimális hitelesítési kód minta

Az alkalmazások Azure-szolgáltatásokkal történő hitelesítéséhez a következő minimális kódot használhatja az ügyfélalkalmazás számára.

Szüksége lesz az *alkalmazás (ügyfél) azonosítójának* és *könyvtárának (BÉRLŐi) azonosítójának* korábbi verziójára, valamint az Azure Digital Twins-példány URL-címére.

> [!TIP]
> Az Azure digitális Twins-példány URL-címe az Azure digitális Twins-példány *állomásneve* *https://* hozzáadásával történik. Az *állomásnév*, valamint a példány összes tulajdonságának megtekintéséhez futtathatja a parancsot `az dt show --dt-name <your-Azure-Digital-Twins-instance>` . A `az account show --query tenantId` parancs használatával megtekintheti a *címtár (bérlő) azonosítóját*. 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

Ne feledje, hogy ha a fenti kód megkeresi az ügyfél-azonosítót, a bérlői azonosítót és a példány URL-címét közvetlenül a kódban az egyszerűség kedvéért, érdemes lehet a kód beolvasni ezeket az értékeket egy konfigurációs fájlból vagy környezeti változóból.

A MSAL számos további lehetőség közül választhat, amelyek olyan dolgok megvalósítására használhatók, mint a gyorsítótárazás és más hitelesítési folyamatok. Erről további információt a [*Microsoft Authentication Library (MSAL) áttekintése*](../active-directory/develop/msal-overview.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

További információ arról, hogyan működik a biztonság az Azure digitális Twinsban:
* [*Fogalmak: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md)

Vagy most, hogy a hitelesítés be van állítva, a következő lépésekkel hozhat létre modelleket a példányban:
* [*Útmutató: egyéni modellek kezelése*](how-to-manage-model.md)