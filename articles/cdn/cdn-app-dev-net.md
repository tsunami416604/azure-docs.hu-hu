---
title: Ismerkedés az Azure CDN-kódtár a .NET-hez |} A Microsoft Docs
description: Ismerje meg, hogyan írhat .NET-alkalmazások kezelése az Azure CDN-t a Visual Studio használatával.
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
ms.openlocfilehash: 838c76e6a383b61ff465f3ed7506af34c8cd01d4
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916614"
---
# <a name="get-started-with-azure-cdn-development"></a>Ismerkedés az Azure CDN-fejlesztéssel
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Használhatja a [Azure CDN-kódtár a .NET-hez](/dotnet/api/overview/azure/cdn) létrehozása és a CDN-profilok és a végpontok felügyeletének automatizálására.  Ez az oktatóanyag végigvezeti egy egyszerű .NET Konzolalkalmazás, amely bemutatja az elérhető műveletek számos létrehozását.  Ebben az oktatóanyagban nem célja, hogy a .NET-hez, részletesen leírja az Azure CDN-kódtár minden aspektusát.

Visual Studio 2015-oktatóanyag elvégzéséhez szüksége lesz.  [A Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) szabadon letölthető.

> [!TIP]
> A [ebből az oktatóanyagból befejezett projekt](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) letölthető az MSDN Webhelyén.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Hozzon létre a projektet, és adja hozzá a Nuget-csomagok
Most, hogy már létre egy erőforráscsoportot a CDN-profilok és kap az Azure AD alkalmazás engedélyt, kezelheti a CDN-profilokat és a csoporton belüli végpontok, hogy megkezdheti az alkalmazás létrehozása.

A Visual Studio 2015-ben kattintson **fájl**, **új**, **projekt...**  az új projekt párbeszédpanel megnyitásához.  Bontsa ki a **Visual C#**, majd **Windows** a bal oldali panelen.  Kattintson a **Konzolalkalmazás** a középső ablaktáblán.  Nevezze el a projektet, majd kattintson a **OK**.  

![Új projekt](./media/cdn-app-dev-net/cdn-new-project.png)

A projekt van fogjuk használni az egyes Azure-könyvtárakban található Nuget-csomagok.  Adjunk hozzá azokat a projekthez.

1. Kattintson a **eszközök** menüben **Nuget-Csomagkezelő**, majd **Package Manager Console**.
   
    ![Nuget-csomagok kezelése](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. A Package Manager Console hajtsa végre a következő paranccsal telepíthető a **Active Directory Authentication Library (ADAL)**:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Hajtsa végre az alábbiakat telepítéséhez a **Azure CDN Management Library**:
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Irányelvek, állandók, metoda main és segédmetódusokat
Folytassuk a program írt alapszintű struktúrát.

1. Vissza a Program.cs lapon cserélje le a `using` elejéhez, a következő irányelveket:
   
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
2. Azt kell meghatároznia az egyes állandókat a módszert fogja használni.  Az a `Program` osztályhoz, de mielőtt a `Main` metódust, adja hozzá a következő.  Ügyeljen arra, hogy cserélje le a zárójelben, beleértve a  **&lt;csúcsos zárójeleket&gt;**, igény szerint a saját értékeire.
   
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
3. A feladatvezérlőben is meghatározhat változókat.  Használjuk ezeket később határozza meg, ha a profil és végpont már létezik.
   
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
5. Az egyéb módszerek közül fog kérni a felhasználót, az "Igen/nem" kérdések.  Adja hozzá a következő metódust megkönnyítéséhez egy kicsit:
   
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

Most, hogy a program az alapszintű struktúrát írt, hogy hozzon létre meghívott módszerek a `Main` metódust.

## <a name="authentication"></a>Authentication
Ahhoz, hogy az Azure CDN Management Library, kell az egyszerű szolgáltatásnév hitelesítése és a egy hitelesítési jogkivonat beszerzése.  Ez a módszer adal-t használja a jogkivonatot beolvasni.

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

Ha használ az egyes felhasználók hitelesítését, a `GetAccessToken` módszer kis mértékben eltérő fog kinézni.

> [!IMPORTANT]
> Ez a kódminta csak akkor használja, ha szeretné, hogy az egyes felhasználói hitelesítés helyett egy egyszerű szolgáltatás kiválasztása.
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

Ne felejtse el `<redirect URI>` az átirányítási URI-JÁNAK Azure AD-ben az alkalmazás regisztrációja során megadott.

## <a name="list-cdn-profiles-and-endpoints"></a>A CDN-profilok listázása és végpontok
Most már készen állunk CDN műveletek végrehajtásához.  A metódus végzi a legfontosabb lista a profilok és a végpontok létrehoztuk az erőforráscsoportot, és ha egyezést talál az állandókat meghatározott profil és -végpont nevének teszi jegyezze fel, amely későbbi használatra így azt nem próbál létrehozni a duplikált elemek.

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

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profilok és a végpontok létrehozása
Ezután hozunk létre egy profilt.

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

A profil létrehozása után egy végpontot hozunk létre.

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
> A fenti példa hozzárendeli a végpont nevű forrás *Contoso* állomásnévvel rendelkező `www.contoso.com`.  Ez a saját forrás gazdaneve átirányítása módosítani kell.
> 
> 

## <a name="purge-an-endpoint"></a>-Végpont végleges törlése
Feltéve, hogy a végpont létrejött, egy gyakori tevékenység, amelyet meg szeretnénk előfordulhat, hogy hajtsa végre a programra van kiürítése a végpont tartalmát.

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
> A példában a karakterlánc a fenti `/*` azt jelzi, hogy szeretnék-e minden, a végpont elérési útja gyökerében kiürítése.  Ez az ellenőrzés egyenértékű **összes kiürítése** az Azure portal "kiürítése" párbeszédpanelen. Az a `CreateCdnProfile` metódus, a profil létrehozott egy **verizon Azure CDN** profilt a kóddal `Sku = new Sku(SkuName.StandardVerizon)`, így ez lesz sikeres.  Azonban **Akamai Azure CDN** profilok nem támogatják a **összes kiürítése**, így ha volt az Akamai-profil használata ebben az oktatóanyagban, akkor van kiürítése egyedi elérési utak közé tartozik.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profilok és a végpontok törlése
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
Azt most fordítsa le és futtassa a programot kattintva a **Start** gomb a Visual Studióban.

![Program futtatása](./media/cdn-app-dev-net/cdn-program-running-1.png)

Amikor a program elérte a fenti parancssor, térjen vissza az erőforráscsoportot az Azure Portalon, és tekintse meg, hogy létrejött-e a profilt kell lennie.

![Sikerült!](./media/cdn-app-dev-net/cdn-success.png)

Majd ellenőrizheti, hogy az utasításokat a többi, a program futtatásához.

![Program befejezése](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>További lépések
A kész projektet ebben a bemutatóban a megtekintéséhez [töltse le a mintát](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

A .NET-hez az Azure CDN Management Library további dokumentációért megkereséséhez tekintse meg a [leírása az MSDN](/dotnet/api/overview/azure/cdn).

A CDN-erőforrások kezelése a [PowerShell](cdn-manage-powershell.md).

