---
title: Ismerkedés a . Microsoft dokumentumok
description: Megtudhatja, hogy miként írhat .NET-alkalmazásokat az Azure CDN visual studio használatával történő kezeléséhez.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7e3ad3a5928b36c221bb83b1c4012c3c9e14f35d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594179"
---
# <a name="get-started-with-azure-cdn-development"></a>Ismerkedés az Azure CDN-fejlesztéssel
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Az Azure [CDN Library for .NET](/dotnet/api/overview/azure/cdn) használatával automatizálhatja a CDN-profilok és végpontok létrehozását és kezelését.  Ez az oktatóanyag egy egyszerű .NET konzolalkalmazás létrehozásán vezet be, amely számos rendelkezésre álló műveletet bemutató.  Ez az oktatóanyag nem ismerteti részletesen az Azure CDN-kódtár részletes leírását.

Az oktatóanyag végrehajtásához a Visual Studio 2015-re van szükség.  [A Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) ingyenesen letölthető.

> [!TIP]
> Az [oktatóanyag befejezett projektje](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) letölthető az MSDN-ről.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>A projekt létrehozása és Nuget csomagok hozzáadása
Most, hogy létrehoztunk egy erőforráscsoportot a CDN-profilokhoz, és az Azure AD-alkalmazásunk engedélyt adott a CDN-profilok és a csoporton belüli végpontok kezelésére, elkezdhetjük az alkalmazás létrehozását.

A Visual Studio 2015-ből kattintson a **Fájl**, **Új**, **Projekt parancsra** az új projekt párbeszédpanel megnyitásához.  Bontsa ki a **Visual C#** csomópontot, majd válassza a **Windows** elemet a bal oldali ablaktáblában.  Kattintson a **konzolalkalmazás** elemre a középső ablaktáblán.  Nevezze el a projektet, majd kattintson **az OK gombra.**  

![Új projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Projektünk a Nuget-csomagokban található néhány Azure-könyvtárat fog használni.  Adjuk hozzá ezeket a projekthez.

1. Kattintson az **Eszközök** menü **Nit csomagkezelő**, majd **a Csomagkezelő konzol parancsra.**
   
    ![Nuget csomagok kezelése](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. A Csomagkezelő konzolon hajtsa végre a következő parancsot az **Active Directory hitelesítési könyvtár (ADAL)** telepítéséhez:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Az Azure CDN Management Library telepítéséhez hajtsa végre az alábbi **parancsot:**
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Irányelvek, állandók, fő módszer és segítő módszerek
Írassuk meg a programunk alapszerkezetét.

1. A Program.cs lapon cserélje `using` le a lap tetején lévő irányelveket a következőkre:
   
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
2. Meg kell határoznunk néhány állandót, amit a módszereink használni fognak.  Az `Program` osztályban, de `Main` a metódus előtt adja hozzá a következőket.  Ügyeljen arra, hogy a helyőrzőket, beleértve a ** &lt;szögletes&gt;zárójeleket**is, szükség szerint cserélje ki a saját értékeire.
   
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
3. Szintén az osztály szintjén határozza meg ezt a két változót.  Ezeket később felhasználjuk annak megállapítására, hogy a profilunk és a végpontunk már létezik-e.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Cserélje `Main` ki a módszert az alábbiak szerint:
   
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
5. Néhány más módszerünk "Igen/Nem" kérdésekkel fogja kérni a felhasználót.  Add hozzá a következő módszert, hogy egy kicsit könnyebb:
   
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

Most, hogy a programunk alapvető szerkezete meg van `Main` írva, meg kell teremtenünk a módszer által hívott módszereket.

## <a name="authentication"></a>Hitelesítés
Mielőtt használhatja az Azure CDN Felügyeleti könyvtár, hitelesítési egyszerű szolgáltatásunkat kell hitelesítenünk, és egy hitelesítési jogkivonatot kell beszerezni.  Ez a módszer a token adal segítségével kéri le az ADAL-t.

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

Ha egyéni felhasználói hitelesítést `GetAccessToken` használ, a metódus kissé eltérő lesz.

> [!IMPORTANT]
> Csak akkor használja ezt a kódmintát, ha az egyszerű szolgáltatás helyett egyéni felhasználói hitelesítést választ.
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

Ügyeljen arra, `<redirect URI>` hogy cserélje le az átirányítási URI-t, amikor regisztrálta az alkalmazást az Azure AD-ben.

## <a name="list-cdn-profiles-and-endpoints"></a>CDN-profilok és végpontok listázása
Most már készen állunk a CDN műveletek végrehajtására.  Az első dolog, amit a módszer nem az összes profil tése az erőforráscsoportban, és ha talál egyezést a profil és a végpont nevek megadva az állandók, tudomásul veszi, hogy a későbbi, így nem próbálja létrehozni ismétlődések.

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

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profilok és végpontok létrehozása
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
> A fenti példa egy *Contoso* nevű origót `www.contoso.com`rendel a végponthoz állomásnévvel.  Ezt úgy kell módosítania, hogy a saját eredetének állomásnevére mutasson.
> 
> 

## <a name="purge-an-endpoint"></a>Végpont kiürítése
Feltételezve, hogy a végpont létrejött, az egyik gyakori feladat, hogy érdemes elvégezni a program törli a tartalmat a végpontunkban.

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
> A fenti példában `/*` a karakterlánc azt jelzi, hogy mindent meg szeretnék tisztítani a végpont elérési útjának gyökerében.  Ez egyenértékű az **Összes kiürítése** az Azure Portal "kiürítése" párbeszédpanelen. A `CreateCdnProfile` módszerben a **Verizon-profilból Azure CDN-ként** `Sku = new Sku(SkuName.StandardVerizon)`létrehozottprofilunkat a kód használatával hoztam létre, így ez sikeres lesz.  Azonban az **Azure CDN aakmai** profilok nem **támogatják purge all**, így ha én egy Akamai profilt ebben az oktatóanyagban, azt kell tartalmaznia konkrét elérési utak at tisztogatás.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profilok és végpontok törlése
Az utolsó módszerek törlik a végpontot és a profilt.

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
Most már lefordíthatjuk és futtathatjuk a programot a Visual Studio **Start** gombjára kattintva.

![Futó program](./media/cdn-app-dev-net/cdn-program-running-1.png)

Amikor a program eléri a fenti kérdést, képesnek kell lennie arra, hogy visszatérjen az erőforráscsoporthoz az Azure Portalon, és láthatja, hogy a profil létrejött.

![Sikerült!](./media/cdn-app-dev-net/cdn-success.png)

Ezután megerősíthetjük a program többi részének futtatására irányuló utasításokat.

![Program befejezése](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Következő lépések
A forgatókönyvből a befejezett projekt megtekintéséhez [töltse le a mintát.](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)

Az Azure CDN Management Library for . [reference on MSDN](/dotnet/api/overview/azure/cdn)

A CDN-erőforrások kezelése a [PowerShell használatával.](cdn-manage-powershell.md)

