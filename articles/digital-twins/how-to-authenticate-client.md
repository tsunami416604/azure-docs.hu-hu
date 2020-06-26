---
title: Ügyfélalkalmazás hitelesítése
titleSuffix: Azure Digital Twins
description: 'Lásd: ügyfélalkalmazás hitelesítése az Azure Digital Twins szolgáltatással.'
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85390822"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Ügyfélalkalmazás hitelesítése az Azure Digital Twins használatával

Miután [létrehozta az Azure Digital Twins-példányt](how-to-set-up-instance.md), létrehozhat egy ügyfélalkalmazás, amelyet a példánnyal való interakcióhoz fog használni. Miután beállította a Starter Client projektet, ez a cikk bemutatja, hogyan hitelesítheti az ügyfélalkalmazás az Azure Digital Twins-példánnyal.

Ez két lépésben történik:
1. Alkalmazás-regisztráció létrehozása
2. Hitelesítési kód írása ügyfélalkalmazás

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>Alkalmazás-regisztráció létrehozása

Ha az Azure Digital Twins szolgáltatással szeretne hitelesítést végezni egy ügyfélalkalmazás használatával, be kell állítania egy **alkalmazás regisztrációját** [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

Az alkalmazás regisztrálása az [Azure Digital Twins API](how-to-use-apis-sdks.md)-khoz való hozzáférési engedélyek konfigurálására szolgál. Az ügyfélalkalmazás hitelesíti az alkalmazás regisztrációját, és ennek eredményeképpen megkapja a konfigurált hozzáférési engedélyeket az API-khoz.

Alkalmazás-regisztráció létrehozásához meg kell adnia az Azure digitális Twins API-k erőforrás-azonosítóit, valamint az API-hoz való alapkonfigurációt. A munkakönyvtárában nyisson meg egy új fájlt, és adja meg a következő JSON-kódrészletet a részletek konfigurálásához: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Mentse ezt a fájlt *manifest.jsként*.

> [!NOTE] 
> Vannak olyan helyek, ahol a "felhasználóbarát" emberi olvashatóságú karakterlánc `https://digitaltwins.azure.net` használható az Azure digitális Twins erőforrás-alkalmazás azonosítójára a GUID helyett `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . A dokumentációban szereplő számos példa például a MSAL-könyvtárral történő hitelesítést használja, és a felhasználóbarát karakterláncot is használhatja erre a célra. Az alkalmazás regisztrációjának létrehozása során azonban az azonosító GUID formáját kell megadnia, mivel a fenti ábrán látható. 

A Cloud Shell ablakban kattintson a "fájlok feltöltése/letöltése" ikonra, és válassza a "feltöltés" lehetőséget.

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="A feltöltési lehetőség kiválasztását ábrázoló Cloud Shell ablak":::
Nyissa meg az imént létrehozott *manifest.js* , és nyomja meg a "Megnyitás" lehetőséget.

Ezután futtassa a következő parancsot egy alkalmazás regisztrációjának létrehozásához (szükség szerint cserélje le a helyőrzőket):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

A parancs kimenete ehhez hasonlóan néz ki.

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="Új HRE-alkalmazás regisztrálása":::

Az alkalmazás regisztrációjának létrehozása után a következő [hivatkozásra](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) kattintva navigáljon a Azure Portal HRE-alkalmazás regisztrációjának áttekintés lapjára.

Ebből az áttekintésből válassza ki az imént létrehozott alkalmazás-regisztrációt a listából. Ekkor megnyílik a részletei egy olyan oldalon, mint a következő:

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Azure Portal: hitelesítési azonosítók":::

Jegyezze **fel a lapon** megjelenő *alkalmazás (ügyfél) azonosítóját* és *KÖNYVTÁRát (bérlői azonosítóját)* . Ezeket az értékeket később fogja használni az ügyfélalkalmazások Azure digitális Twins API-kkal való hitelesítéséhez.

> [!NOTE]
> A forgatókönyvtől függően előfordulhat, hogy további módosításokat kell végeznie az alkalmazás regisztrálásakor. Íme néhány gyakori követelmény, amely a következőkhöz szükséges:
> * Nyilvános ügyfél-hozzáférés aktiválása
> * A webes és asztali hozzáférés adott válasz URL-címeinek beállítása
> * Implicit OAuth2 hitelesítési folyamatok engedélyezése
> * Ha az Azure-előfizetést egy Microsoft-fiók, például az élő, az Xbox vagy a Hotmail használatával hozza létre, a személyes fiókok támogatásához be kell állítania a *signInAudience* az alkalmazás regisztrálásához.
> Ezeket a beállításokat a legegyszerűbben úgy állíthatja be, ha a [Azure Portal](https://portal.azure.com/)használja. További információ erről a folyamatról: [Alkalmazások regisztrálása a Microsoft Identity platformmal](https://docs.microsoft.com/graph/auth-register-app-v2).

## <a name="write-client-app-authentication-code-net-c-sdk"></a>Ügyfél-alkalmazás hitelesítési kódjának írása: .NET (C#) SDK

Ez a szakasz azt a kódot írja le, amelyet az ügyfélalkalmazás tartalmazni kell, hogy a .NET (C#) SDK használatával lehessen végrehajtani a hitelesítési folyamatot.
Az Azure Digital Twins C# SDK a .NET-hez készült Azure SDK része. Itt található: az [Azure IoT Digital Twin ügyféloldali kódtára a .net-hez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).

### <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik már beállított alapszintű ügyfélalkalmazás-projekttel, hozzon létre egy alapszintű .NET-projektet, amelyet ezzel az Oktatóanyaggal szeretne használni.

A .NET SDK használatához a következő csomagokat kell tartalmaznia a projektben:
* `Azure.DigitalTwins.Core`(verzió `1.0.0-preview.2` )
* `Azure.Identity`

A választott eszközöktől függően a Visual Studio Package Managerrel vagy a parancssori eszközzel teheti meg `dotnet` . 

### <a name="authentication-and-client-creation-net"></a>Hitelesítés és ügyfél létrehozása: .NET

A .NET SDK-val történő hitelesítéshez használja az [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) könyvtárban definiált hitelesítő adatok beszerzésére szolgáló metódusok egyikét.

Az alábbi két módszer általában használatban van: 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). Ez a módszer interaktív alkalmazások számára készült, és a rendszer egy webböngészőt hoz létre a hitelesítéshez.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). Ez a módszer nagyban használható olyan esetekben, ahol [felügyelt identitásokra (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)van szükség – például a Azure functions használatakor. 

A következő utasításokra is szüksége lesz:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

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

Az Azure-függvényekben a felügyelt identitás hitelesítő adatait használhatja a következőhöz hasonló módon:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

További információ [: Azure-függvény beállítása az adatok feldolgozásához](how-to-create-azure-function.md) a függvények kontextusában a fontos konfigurációs beállítások némelyikét bemutató részletesebb példa.

Továbbá a függvények hitelesítésének használatához ne felejtse el a következőket:
* [Felügyelt identitás engedélyezése](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Környezeti változók](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* Rendeljen engedélyeket a functions alkalmazáshoz, amely lehetővé teszi, hogy hozzáférjen a digitális Twins API-khoz. További információkért lásd [: útmutató: Azure-függvény beállítása az adatok feldolgozásához](how-to-create-azure-function.md) .

## <a name="authentication-in-an-autorest-generated-sdk"></a>Hitelesítés az autorest által generált SDK-ban

Ha nem a .NET-et használja, dönthet úgy, hogy az SDK-függvénytárat tetszőleges nyelven felépíti, ahogy azt a [útmutató: egyéni SDK-k létrehozása az Azure Digital Twins](how-to-create-custom-sdks.md)szolgáltatáshoz az autorest használatával című témakör ismerteti.

Ez a szakasz azt ismerteti, hogyan lehet hitelesítést végezni ebben az esetben.

### <a name="prerequisites"></a>Előfeltételek

Ez a példa egy autorest használatával generált írógéppel SDK-t használ. Ennek eredményeképpen az alábbiakra is szükség van:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-Rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Minimális hitelesítési kód minta

Ha .NET-alkalmazást szeretne hitelesíteni az Azure-szolgáltatásokkal, a következő minimális kódot használhatja az ügyfélalkalmazás számára.

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

A MSAL számos további lehetőség közül választhat, amelyek olyan dolgok megvalósítására használhatók, mint a gyorsítótárazás és más hitelesítési folyamatok. Erről további információt a [Microsoft Authentication Library (MSAL) áttekintése](../active-directory/develop/msal-overview.md)című témakörben talál.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan működik a biztonság az Azure digitális Twinsban:
* [Fogalmak: az Azure Digital Twins-megoldások biztonsága](concepts-security.md)

Vagy most, hogy a hitelesítés be van állítva, a következő lépésekkel hozhat létre modelleket a példányban:
* [Útmutató: egyéni modellek kezelése](how-to-manage-model.md)