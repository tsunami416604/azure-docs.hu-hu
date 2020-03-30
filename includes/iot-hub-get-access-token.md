---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70803006"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Azure Resource Manager-token beszerzése
Az Azure Active Directorynak hitelesítenie kell az erőforrásokon az Azure Resource Manager használatával végrehajtott összes feladatot. Az itt látható példa jelszó-hitelesítést használ, más megközelítésekhez [lásd: Az Azure Resource Manager-kérelmek hitelesítése.][lnk-authenticate-arm]

1. Adja hozzá a következő kódot a **fő** metódus hoz Program.cs egy jogkivonatot az Azure AD-ből az alkalmazásazonosító és a jelszó használatával.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Hozzon létre egy **ResourceManagementClient** objektumot, amely a jogkivonatot használja a következő kód hozzáadásával a **fő** metódus végéhez:
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. A használt erőforráscsoport létrehozása vagy hivatkozás ának beszerzése:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx