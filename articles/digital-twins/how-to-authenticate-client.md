---
title: Alkalmazáshitelesítési kód írása
titleSuffix: Azure Digital Twins
description: 'Lásd: hitelesítési kód írása ügyfélalkalmazás'
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f2cef34413f46608e8bc35a009a29212af5ddf20
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893594"
---
# <a name="write-client-app-authentication-code"></a>Ügyfél-alkalmazás hitelesítési kódjának írása

Miután [beállított egy Azure digitális Twins-példányt és-hitelesítést](how-to-set-up-instance-portal.md), létrehozhat egy ügyfélalkalmazás, amelyet a példánnyal való interakcióhoz használni fog. Miután beállította az alapszintű ügyfél-projektet, meg kell adnia egy **kódot az ügyfél-alkalmazásban** az Azure Digital Twins-példányon való hitelesítéshez.

Az Azure Digital Twins az [Azure ad biztonsági jogkivonatok használatával végzi a hitelesítést az OAUTH 2,0 alapján](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims). Az SDK hitelesítéséhez be kell szereznie egy tulajdonosi jogkivonatot a megfelelő engedélyekkel az Azure digitális Twins-hoz, és át kell adnia az API-hívásokkal együtt. 

Ez a cikk bemutatja, hogyan szerezhet be hitelesítő adatokat az `Azure.Identity` ügyféloldali kódtár használatával. Ez a cikk a C#-ban található példákat mutatja be, például a [.net (c#) SDK](https://www.nuget.org/packages/Azure.DigitalTwins.Core)-hoz írt változatot, `Azure.Identity` függetlenül attól, hogy milyen SDK-t használ (az Azure Digital Twins-hoz elérhető SDK-kkal kapcsolatos további információkért lásd [*: útmutató: az Azure digitális Twins API-k és SDK-k használata*](how-to-use-apis-sdks.md)).

## <a name="prerequisites"></a>Előfeltételek

Először végezze el a telepítési lépéseket a következő [*útmutatóban: példány és hitelesítés beállítása*](how-to-set-up-instance-portal.md). Ez biztosítja, hogy rendelkezik egy Azure digitális Twins-példánnyal, a felhasználó rendelkezik hozzáférési engedélyekkel, és beállította az ügyfélalkalmazások engedélyeit. Az összes beállítás után készen áll az ügyfélalkalmazás kódjának írására.

A folytatáshoz szüksége lesz egy ügyfélalkalmazás-projektre, amelyben a kódot írja. Ha még nem rendelkezik beállított ügyfélalkalmazás-projekttel, hozzon létre egy alapszintű projektet a választott nyelven az oktatóanyag használatával.

## <a name="common-authentication-methods-with-azureidentity"></a>Általános hitelesítési módszerek az Azure. Identityvel

`Azure.Identity` a egy ügyféloldali kódtár, amely több hitelesítő adatokat biztosít a tulajdonosi jogkivonatok beszerzéséhez és az SDK-val való hitelesítéshez. Bár ez a cikk példákat tartalmaz a C#-ban, több nyelv is megtekinthető `Azure.Identity` , többek között...
* [.NET (C#)](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

Három gyakori hitelesítő adat – a metódusok beszerzése `Azure.Identity` :
* A [DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) az `TokenCredential` Azure-ban üzembe helyezett alkalmazások alapértelmezett hitelesítési folyamatát biztosítja, és **a helyi fejlesztéshez ajánlott választás**. Emellett engedélyezhető az ebben a cikkben javasolt két módszer kipróbálására is. a rendszer becsomagolja `ManagedIdentityCredential` és hozzáfér `InteractiveBrowserCredential` egy konfigurációs változóhoz.
* A [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) remekül működik olyan esetekben, amikor [felügyelt identitásokat (MSI)](../active-directory/managed-identities-azure-resources/overview.md)kell használnia, és jó választás a Azure functions és az Azure-szolgáltatások üzembe helyezésére.
* A [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) interaktív alkalmazásokhoz készült, és használható egy hitelesített SDK-ügyfél létrehozásához.

Az alábbi példa bemutatja, hogyan használhatja ezeket a .NET (C#) SDK-val.

## <a name="authentication-examples-net-c-sdk"></a>Hitelesítési példák: .NET (C#) SDK

Ez a szakasz egy példát mutat be a C#-ban, hogy a megadott .NET SDK-t használja a hitelesítési kód írásához.

Először is vegye fel az SDK `Azure.DigitalTwins.Core` -csomagot és a `Azure.Identity` csomagot a projektbe. A választott eszközöktől függően a Visual Studio Package Managerrel vagy a parancssori eszközzel is megadhatja a csomagokat `dotnet` . 

A következő using utasításokat is hozzá kell adnia a projekt kódjához:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Ezután adjon hozzá programkódot a hitelesítő adatok beszerzéséhez a egyik metódusának használatával `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential metódus

A [DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) az `TokenCredential` Azure-ban üzembe helyezett alkalmazások alapértelmezett hitelesítési folyamatát biztosítja, és **a helyi fejlesztéshez ajánlott választás**.

Az alapértelmezett Azure-beli hitelesítő adatok használatához szüksége lesz az Azure Digital Twins-példány URL-címére (a[keresendő utasítások](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Az alábbi mintakód a projekthez való hozzáadását írja elő `DefaultAzureCredential` :

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential metódus

A [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) metódus nagyban működik olyan esetekben, ahol [felügyelt identitásokra (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)van szükség – például a Azure functions használatakor.

Ez azt jelenti, hogy `ManagedIdentityCredential` a `DefaultAzureCredential` `InteractiveBrowserCredential` projekt egy másik részének hitelesítéséhez ugyanazt a projektet használhatja, mint a vagy a.

Az alapértelmezett Azure-beli hitelesítő adatok használatához szüksége lesz az Azure Digital Twins-példány URL-címére (a[keresendő utasítások](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Az Azure-függvényekben a felügyelt identitás hitelesítő adatait használhatja a következőhöz hasonló módon:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential metódus

Az [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) metódus interaktív alkalmazásokhoz készült, és egy webböngészőt hoz létre a hitelesítéshez. Ezt felhasználhatja `DefaultAzureCredential` olyan esetekben is, ahol interaktív hitelesítést igényel.

Az interaktív böngésző hitelesítő adatainak használatához szüksége lesz egy alkalmazás- **regisztrációra** , amely jogosult az Azure Digital Twins API-khoz. Az alkalmazás regisztrálásának lépéseivel kapcsolatban lásd: az [*ügyfélalkalmazások hozzáférési engedélyeinek beállítása*](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) szakasz, *útmutató: példány és hitelesítés beállítása*. Ha az alkalmazás regisztrálása be van állítva, szüksége lesz...
* az alkalmazás regisztrációs *alkalmazásának (ügyfél) azonosítója*
* az alkalmazás regisztrációs *címtára (bérlője) azonosítója*
* Az Azure digitális Twins-példány URL-címe (a[keresendő utasítások](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Íme egy példa arra a kódra, amellyel hitelesített SDK-ügyfelet hozhat létre a használatával `InteractiveBrowserCredential` .

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

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

#### <a name="other-notes-about-authenticating-azure-functions"></a>Egyéb megjegyzések a Azure Functions hitelesítéséhez

További információ [*: Azure-függvény beállítása az adatok feldolgozásához*](how-to-create-azure-function.md) a függvények kontextusában a fontos konfigurációs beállítások némelyikét bemutató részletesebb példa.

Továbbá a függvények hitelesítésének használatához ne felejtse el a következőket:
* [Felügyelt identitás engedélyezése](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* A megfelelő [környezeti változók](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) használata
* Rendeljen engedélyeket a functions alkalmazáshoz, amely lehetővé teszi, hogy hozzáférjen a digitális Twins API-khoz. Azure Functions folyamatokkal kapcsolatos további információkért lásd: [*útmutató: Azure-függvény beállítása az adatok feldolgozásához*](how-to-create-azure-function.md).

## <a name="other-credential-methods"></a>Egyéb hitelesítőadat-módszerek

Ha a fenti, Kiemelt hitelesítési forgatókönyvek nem fedik le az alkalmazás igényeit, megismerheti a [**Microsoft Identity platformon**](../active-directory/develop/v2-overview.md#getting-started)kínált egyéb hitelesítési módokat is. A platform dokumentációja a további hitelesítési forgatókönyvekre vonatkozik, az alkalmazás típusa szerint rendezve.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan működik a biztonság az Azure digitális Twinsban:
* [*Fogalmak: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md)

Vagy most, hogy a hitelesítés be van állítva, a következő lépésekkel hozhat létre modelleket a példányban:
* [*Útmutató: egyéni modellek kezelése*](how-to-manage-model.md)
