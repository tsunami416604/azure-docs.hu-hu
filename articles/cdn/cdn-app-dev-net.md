---
title: Ismerkedés az Azure CDN könyvtár a .NET-hez |} Microsoft Docs
description: Ismerje meg, hogyan írhat .NET-alkalmazások kezelése az Azure CDN Visual Studio használatával.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5379586355ece98af6295236d6cbd09cb31c742b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
ms.locfileid: "23843048"
---
# <a name="get-started-with-azure-cdn-development"></a>Ismerkedés az Azure CDN-fejlesztéssel
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Használhatja a [Azure CDN .NET-keretrendszerhez készült](https://msdn.microsoft.com/library/mt657769.aspx) létrehozása és a CDN-profil és a végpontok automatizálására.  Ez az oktatóanyag bemutatja, hogyan kell létrehozni egy egyszerű .NET Konzolalkalmazás azt mutatja be a rendelkezésre álló műveletek számos.  Ez az oktatóanyag nem célja, hogy a .NET-hez, részletesen leírja az Azure CDN kódtár minden szempontját.

Az oktatóanyag teljesítéséhez a Visual Studio 2015 van szüksége.  [A Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) szabadon letölthető.

> [!TIP]
> A [az oktatóanyagot befejezett projekt](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) letölthető az MSDN Webhelyén.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>A projekt létrehozása, és adja hozzá a Nuget-csomagok
Most, hogy előre létrehozott erőforráscsoport a CDN-profil és a CDN-profil és a csoporton belüli végpontok kezelése az Azure AD-alkalmazás engedélyt kap, nem lehet elindítani, az alkalmazás létrehozása.

A Visual Studio 2015-öt, belül kattintson **fájl**, **új**, **projekt...**  a új projekt párbeszédpanel megnyitásához.  Bontsa ki a **Visual C#**, majd jelölje be **Windows** a bal oldali ablaktáblán.  Kattintson a **Konzolalkalmazás** a középső ablaktáblán.  Nevezze el a projektet, majd kattintson az **OK**.  

![Új projekt](./media/cdn-app-dev-net/cdn-new-project.png)

A projekt üzemeltetéséhez kívánja használni a Nuget-csomagok szereplő Azure könyvtárak.  Adjuk hozzá azokat a projekthez.

1. Kattintson a **eszközök** menü **Nuget-Csomagkezelő**, majd **Csomagkezelő konzol**.
   
    ![Nuget-csomagok kezelése](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. A Package Manager Console hajtható végre a következő paranccsal telepíthető a **Active Directory Authentication Library (ADAL)**:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Hajtsa végre az alábbiakat telepíti a **Azure CDN könyvtár**:
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Irányelvek, állandók, fő metódus és segédmódszereket
Folytassuk a program írása alapvető szerkezete.

1. Vissza a Program.cs lapon cserélje le a `using` irányelvek a lap tetején a következőre:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. Adja meg az egyes állandók a módszerek fogja használni kell.  Az a `Program` osztály, de előtte a `Main` metódus, adja hozzá a következő.  Ügyeljen arra, hogy cserélje le a helyőrzőket, beleértve a  **&lt;csúcsos zárójelek&gt;**, igény szerint a saját értékekkel.
   
    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";
   
    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Az osztály szinten is definiálhat változókat.  Használjuk ezeket később határozza meg, ha a profil és -végpont már létezik.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Cserélje le a `Main` módszert az alábbiak szerint:
   
   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();
   
       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };
   
       ListProfilesAndEndpoints(cdn);
   
       // Create CDN Profile
       CreateCdnProfile(cdn);
   
       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);
   
       Console.WriteLine();
   
       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);
   
       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);
   
       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);
   
       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Az egyéb módszerek közül fog kérni a felhasználót, az "Igen/nem" kérdésekre.  Adja hozzá a következő metódust könnyebben, amely kissé:
   
    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Most, hogy a program alapvető szerkezete írása, igazolnia kell létrehoznia a módszerek által meghívott a `Main` metódust.

## <a name="authentication"></a>Hitelesítés
Az Azure CDN könyvtár használhatunk, igazolnia kell a szolgáltatás egyszerű hitelesítést, és egy hitelesítési jogkivonat beszerzése.  Ez a módszer adal-t használja a-jogkivonatot lekérdezni.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Ha egyéni felhasználói hitelesítést használ a `GetAccessToken` metódus némileg eltérő fog kinézni.

> [!IMPORTANT]
> Ha használja a kódmintában a felhasználói hitelesítés helyett egy egyszerű szolgáltatás kiválasztása.
> 
> 

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Ügyeljen arra, hogy a csere `<redirect URI>` és az átirányítási URI-t az Azure ad-ben az alkalmazás regisztrálásakor megadott.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista CDN-profil és -végpontok
Most azt készen áll a CDN-műveletek végrehajtásához.  Elsőként a metódus végzi lista a profilok és a végpontok az erőforráscsoportban, és ha egyezést talál az állandókat meghatározott profil és -végpont nevének válik, jegyezze fel, amelyek később, azt ne hozzon létre duplikált elemek.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profil és a végpontok létrehozása
Ezután létrehozunk egy profilt.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

A profil létrehozása után létrehozunk egy végpontot.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> A fenti példa rendeli hozzá a végpont egy eredeti adatforrást nevű *Contoso* állomásnévvel rendelkező `www.contoso.com`.  Meg kell változtatni ezt úgy, hogy a saját forrásállomásnév mutasson.
> 
> 

## <a name="purge-an-endpoint"></a>A végpont törlése
Feltéve, hogy a végpont létrehozását, egy közös feladat, amely azt szeretnénk, előfordulhat, hogy a program a végrehajtásához a végpont tartalmának van kiürítése.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> A karakterlánc a fenti példában a `/*` azt jelzi, hogy kívánt kiürítése mindent gyökérkönyvtárában található a végpont elérési útja.  Ez az ellenőrzés egyenértékű **összes kiürítése** az Azure-portálon "kiürítése" párbeszédpanelen. Az a `CreateCdnProfile` módszer, mint a profil létrehozott egy **Azure CDN Verizon** profilhoz, a kódot `Sku = new Sku(SkuName.StandardVerizon)`, így ez sikeres lesz.  Azonban **Akamai Azure CDN** profilok nem támogatják a **összes kiürítése**, így volt egy Akamai profilt alkalmaz az oktatóanyag, lenne szükség meghatározott elérési útjainak tartalmazza.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profil és a végpontok törlése
Az utolsó módszerek törli a végpont és a profilt.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>A program futtatása
A Microsoft most fordítási és kattintva a program futtatható a **Start** gombra a Visual Studio.

![A program fut](./media/cdn-app-dev-net/cdn-program-running-1.png)

Amikor a program a fenti parancssor eléri, térjen vissza az erőforráscsoportot az Azure portálon, és tekintse meg, hogy létrejött-e a profil kell lennie.

![Sikerült!](./media/cdn-app-dev-net/cdn-success.png)

Azt is erősítse meg a paraméter, a program a többi szükséges.

![Program befejezése](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>További lépések
Ez a forgatókönyv a befejezett projekt megjelenítéséhez [a minta letöltéséhez](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

További dokumentáció az Azure CDN könyvtár a .NET találja, tekintse meg a [hivatkozást az MSDN-en](https://msdn.microsoft.com/library/mt657769.aspx).

A CDN erőforrások kezelése [PowerShell](cdn-manage-powershell.md).

