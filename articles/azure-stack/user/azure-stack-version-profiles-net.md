---
title: API-verzióprofilok használata a .NET SDK-t az Azure Stackben |} A Microsoft Docs
description: Ismerje meg az API-verzióprofilok használata a .NET-tel az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 35329468ee01d5b70d654c1eb4a908db9d3fcb5d
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184829"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>API-verzióprofilok használata a .NET-tel az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

A .NET SDK-t az Azure Stack Resource Manager biztosít eszközöket és az infrastruktúra kezelését. Az SDK-t az erőforrás-szolgáltatók közé tartozik a számítási, hálózatkezelési, tárolási, alkalmazásszolgáltatások, és [KeyVault](../../key-vault/key-vault-whatis.md). A .NET SDK 14 NuGet-csomagok, amelyek le kell tölteni a projekt megoldáshoz minden alkalommal, amikor a profil információkat tartalmaz. Azonban kifejezetten letöltheti melyik erőforrás-szolgáltatót fogja használni a 2018-03-01-hibrid vagy 2017-03-09-profile annak érdekében, hogy az alkalmazás memóriájának optimalizálása. Minden csomag áll egy erőforrás-szolgáltató, a megfelelő API-verzió és az API-profilt, amelyhez tartozik. A .NET SDK API-profilok engedélyezése a hibrid felhőalapú fejlesztés segít a globális Azure-erőforrások és az Azure Stacken erőforrások közötti váltáshoz.

## <a name="net-and-api-version-profiles"></a>.NET- és API-verzióprofilok

Egy API-profil az erőforrás-szolgáltatók és API-verziók. Az API-profilok segítségével egy erőforrás-szolgáltatói csomag az egyes erőforrástípusok legújabb és legnagyobb stabil verziójának beszerzéséhez.

-   Az összes szolgáltatás legújabb verzióját használni, hogy a **legújabb** profil olyan csomagot. Ez a profil része a **Microsoft.Azure.Management** NuGet-csomagot.

-   Az Azure Stack kompatibilis szolgáltatások használatához a **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** vagy **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** csomagokat.

    -   Nincsenek mindegyik erőforrás-szolgáltató az egyes profilok két csomagot.

    -   Ügyeljen arra, hogy a **ResourceProvider** a fenti NuGet-csomag része a megfelelő szolgáltató értékűre változik.

-   A szolgáltatás legújabb API-verzióját használja, használja a **legújabb** profiljának adott NuGet-csomag. Például, ha használni szeretné a **legújabb API-** a számítási szolgáltatás önálló, használja a **legújabb** profiljának a **számítási** csomagot. A **legújabb** profil része a **Microsoft.Azure.Management** NuGet-csomagot.

-   API-verzióját az adott erőforrás-szolgáltató az erőforrástípushoz az adott API-verziók a csomagon belül definiált használatához.

Vegye figyelembe, hogy egyesítheti az összes beállítás ugyanabban az alkalmazásban.

## <a name="install-the-azure-net-sdk"></a>Az Azure .NET SDK telepítése

1.  A Git telepítése. Útmutatásért lásd: [Első lépések – a Git telepítése][].

2.  A megfelelő NuGet-csomagok telepítése: [keresés és a egy csomag telepítése][].

3.  A csomagokat, amelyeket telepíteni kell a használni kívánt profilt verziójától függ. A csomag nevét, a profil-verziók a következők:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  A megfelelő NuGet-csomagok telepítése a Visual Studio Code, tekintse meg a következő hivatkozásra kattintva töltse le a [NuGet-Csomagkezelő utasítások][].

5.  Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi felhasználás céljából. Hozzon létre egy előfizetést az utasításokért lásd: [ajánlatok, előfizetések létrehozása az Azure Stackben][].

6.  Hozzon létre egy egyszerű szolgáltatást, és mentse az ügyfél-Azonosítót és a titkos Ügyfélkulcsot. Az Azure stack-beli szolgáltatásnév létrehozása az utasításokért lásd: [Alkalmazások elérése az Azure Stackhez][]. Vegye figyelembe, hogy az ügyfél-azonosító néven is ismert az Alkalmazásazonosítót egyszerű szolgáltatás létrehozásakor.

7.  Ellenőrizze, hogy az egyszerű szolgáltatást a közreműködői és tulajdonosi szerepkör-előfizetésében. Szerepkör hozzárendelése egyszerű szolgáltatást, lásd: [Alkalmazások elérése az Azure Stackhez][].

## <a name="prerequisites"></a>Előfeltételek

Azure .NET SDK használata az Azure Stack használatával, adja meg a következő értékeket, és adja meg az értékeket a környezeti változókat. A környezeti változók beállítása, tekintse meg az alábbi táblázat az operációs rendszerének utasításokat.

| Érték                     | Környezeti változók   | Leírás                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Bérlőazonosító                 | AZURE_TENANT_ID       | Az Azure Stack értékét [ *bérlőazonosító*][].                                                                          |
| Ügyfél-azonosító                 | AZURE_CLIENT_ID       | A szolgáltatás egyszerű Alkalmazásazonosítót az egyszerű szolgáltatás létrehozásakor ez a cikk az előző szakaszban mentett. |
| Előfizetés azonosítója           | AZURE_SUBSCRIPTION_ID | A [ *előfizetés-azonosító* ][] van, hogy miként férhetnek hozzá az ajánlatok az Azure Stackben.                                                      |
| Titkos ügyfélkulcs             | AZURE_CLIENT_SECRET   | A szolgáltatás egyszerű alkalmazás titkos kulcs mentése az egyszerű szolgáltatás létrehozásakor.                                      |
| Resource Manager-végpont | ARM_ENDPOINT           | Lásd: [ *az Azure Stack resource manager-végpont*][].                                                                    |

Az Azure stack a bérlő Azonosítójának megkereséséhez kövesse az utasításokat, található [Itt](../azure-stack-csp-ref-operations.md). A környezeti változók beállítása, tegye a következőket:

### <a name="microsoft-windows"></a>A Microsoft Windows

A környezeti változók beállítása egy Windows parancssorban, használja a következő formátumot:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux és Unix-alapú rendszerek

Unix-alapú rendszerekben használhatja a következő parancsot:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Az Azure Stack resource manager-végpont

A Microsoft Azure Resource Manager-keretrendszert, amely lehetővé teszi a rendszergazdák üzembe helyezése, kezelése és monitorozása az Azure-erőforrások számára. Az Azure Resource Manager képes kezelni ezeket a feladatokat, csoportként, nem pedig külön-külön, egyetlen művelettel.

A metaadat-információkat is kérhet a Resource Manager-végpontot. A végpont egy JSON-fájlt a kód futtatásához szükséges adatokat adja vissza.

Vegye figyelembe az alábbiakat:

- A **ResourceManagerUrl** van az Azure Stack Development Kit (ASDK): https://management.local.azurestack.external/

- A **ResourceManagerUrl** integrált rendszerek van: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` a szükséges metaadatok lekérése céljából: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

JSON-mintafájlt:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Meglévő API-profilok

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**: az Azure Stackhez készült legújabb profil. Ez a profil Services kompatibilis az Azure Stack mindaddig, amíg a 1808 blokk vagy a további használhatják.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**: Ha egy alacsonyabb, mint a 1808 build stamp, használja ezt a profilt.

3.  **Legújabb**: az összes szolgáltatást a legújabb verziókat álló profilt. Az összes szolgáltatást a legújabb verziókat használhatja. Ez a profil része a **Microsoft.Azure.Management** NuGet-csomagot.

Az Azure Stacket és API-profilokkal kapcsolatos további információkért lásd: egy [API-profilok összefoglalása][].

## <a name="azure-net-sdk-api-profile-usage"></a>Azure .NET SDK API-profil-használat

A következő kódot egy profil ügyfél vezérlőként használandó. Ez a paraméter csak akkor szükséges, az Azure Stack vagy egyéb privát felhők. Globális Azure alapértelmezés szerint ezek a beállítások már rendelkezik.

A következő kódot az Azure Stacken a szolgáltatásnevet hitelesítésére van szükség. A bérlő által a jogkivonat-azonosító és a hitelesítési alap, amelyet kifejezetten az Azure Stack hoz létre.

```csharp
public class CustomLoginCredentials : ServiceClientCredentials
{
    private string clientId;
    private string clientSecret;
    private string resourceId;
    private string tenantId;

    private const string authenticationBase = "https://login.windows.net/{0}";

    public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
    {
        clientId = servicePrincipalId;
        clientSecret = servicePrincipalSecret;
        resourceId = azureEnvironmentResourceId;
        tenantId = azureEnvironmentTenandId;
    }
```

Ez lehetővé teszi, hogy az alkalmazás telepítése sikeresen megtörtént az Azure Stack az API-profil NuGet-csomagok segítségével.

## <a name="define-azure-stack-environment-setting-functions"></a>Azure Stack-környezet beállítás függvények definiálása

Az egyszerű szolgáltatás az Azure Stack környezettel való hitelesítéshez, használja a következő kódot:

```csharp
private string AuthenticationToken { get; set; }
public override void InitializeServiceClient<T>(ServiceClient<T> client)
{
    var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
    var credential = new ClientCredential(clientId, clientSecret);
    var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
    clientCredential: credential).Result;
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    AuthenticationToken = result.AccessToken;
}
```

Ez a művelet a inicializálása szolgáltatásügyfél hitelesítéséhez az Azure Stackhez.

## <a name="samples-using-api-profiles"></a>API-profilok használatával minták

A következő minták GitHub-adattárak található megoldások létrehozása a .NET-keretrendszer és az Azure Stack API profilok referenciaként is használhatja.

-   [A virtuális gép, virtuális hálózatok közötti, erőforráscsoportok és storage-fiókot a tesztelő projektet][]
-   .NET-tel, a virtuális gépek kezelése

### <a name="sample-unit-test-project"></a>Minta egység tesztelő projektet 

1.  Klónozza a tárházat, a következő paranccsal:

    `git clone <https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject>`

2.  Azure-beli szolgáltatásnév létrehozása, és rendelje hozzá egy szerepkörhöz az előfizetés eléréséhez. Egyszerű szolgáltatás létrehozásával kapcsolatos útmutatóért lásd: [Az Azure PowerShell használatával egy egyszerű szolgáltatás létrehozása tanúsítvánnyal][].

3.  A következő szükséges értékek beolvasása:

    1.  Bérlőazonosító
    2.  Ügyfél-azonosító
    3.  Titkos ügyfélkulcs
    4.  Előfizetés azonosítója
    5.  Resource Manager-végpont

4.  Állítsa be az alábbi környezeti változókat a parancssor használatával létrehozott egyszerű szolgáltatás lekért információk segítségével:

    1.  Exportálás AZURE_TENANT_ID = {a bérlő azonosítóját}
    2.  Exportálás AZURE_CLIENT_ID = {az ügyfél-azonosító}
    3.  Exportálás AZURE_CLIENT_SECRET = {az Ügyfélkód}
    4.  Exportálás AZURE_SUBSCRIPTION_ID = {az előfizetés azonosítója}
    5.  Exportálás ARM_ENDPOINT = {az Azure Stack Resource manager URL-cím}

   A Windows, használja **beállítása** helyett **exportálása**.

5.  Győződjön meg arról, a hely változó beállítása az Azure Stack-helyre. Ha például a helyi = "local".

6.  Állítsa be az egyéni bejelentkezési hitelesítő adatait, amely lehetővé teszi, hogy az Azure Stackhez hitelesítéséhez. Vegye figyelembe, hogy ez a kód része szerepel-e ez a minta az engedélyezési mappában.

   ```csharp
   public class CustomLoginCredentials : ServiceClientCredentials
   {
       private string clientId;
       private string clientSecret;
       private string resourceId;
       private string tenantId;
       private const string authenticationBase = "https://login.windows.net/{0}";
       public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
       {
           clientId = servicePrincipalId;
           clientSecret = servicePrincipalSecret;
           resourceId = azureEnvironmentResourceId;
           tenantId = azureEnvironmentTenandId;
       }
   private string AuthenticationToken { get; set; }
   ```

7.  Adja hozzá a következő kódot, ha az Azure Stack használatával bírálja felül a inicializálása szolgáltatásügyfél hitelesítéséhez az Azure Stackhez. Vegye figyelembe, hogy a kód egy része már szerepel ebben a példában az engedélyezési mappában.

   ```csharp
   public override void InitializeServiceClient<T>(ServiceClient<T> client)
   {
      var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
      var credential = new ClientCredential(clientId, clientSecret);
      var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
                clientCredential: credential).Result;
      if (result == null)
      {
          throw new InvalidOperationException("Failed to obtain the JWT token");
      }
      AuthenticationToken = result.AccessToken;
   }
   ```
 
8.  A NuGet Package Manager használatával, keresse meg a "2018-03-01-hibrid", és telepítse a számítási, hálózatkezelési, tárolási, KeyVault és App Services erőforrás-szolgáltatókat a profilhoz tartozó csomagokat.

2.  A .cs fájlban feladatokon belül állítsa be a paramétereket, amelyek szükségesek az Azure Stack használatával működnek. Egy példa látható módon a tevékenység `CreateResourceGroupTest`:

   ```csharp
   var location = Environment.GetEnvironmentVariable("AZURE_LOCATION");
   var baseUriString = Environment.GetEnvironmentVariable("AZURE_BASE_URL");
   var resourceGroupName = Environment.GetEnvironmentVariable("AZURE_RESOURCEGROUP");
   var servicePrincipalId = Environment.GetEnvironmentVariable("AZURE_CLIENT_ID");
   var servicePrincipalSecret = Environment.GetEnvironmentVariable("AZURE_CLIENT_SECRET");
   var azureResourceId = Environment.GetEnvironmentVariable("AZURE_RESOURCE_ID");
   var tenantId = Environment.GetEnvironmentVariable("AZURE_TENANT_ID");
   var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
   var credentials = new CustomLoginCredentials(servicePrincipalId, servicePrincipalSecret, azureResourceId, tenantId);
   ```

1.  A jobb gombbal minden egyes tevékenységhez, és válassza ki a **teszt futtatása**.

    1.  A zöld jelölők oldalsó ablaktábla ablakban figyelmeztet, hogy minden egyes feladat létrehozása sikerült a megadott paraméterek alapján. Tekintse meg az Azure Stack-előfizetéshez, annak érdekében, hogy az erőforrások létrehozása sikerült.

    2.  Egységtesztek futtatásával kapcsolatos további információkért lásd: [A tesztelés-Explorerrel futtatni az egységteszteket.][]

## <a name="next-steps"></a>További lépések

API-profilokkal kapcsolatos további információkért lásd:

- [Az Azure Stackben API-verzióprofilok kezelése](azure-stack-version-profiles.md)
- [Erőforrás-szolgáltató API-ja verziókat támogatja profilok](azure-stack-profiles-azure-resource-manager-versions.md)

  [Első lépések – a Git telepítése]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Keresés és a egy csomag telepítése]: /nuget/tools/package-manager-ui
  [NuGet-Csomagkezelő utasítások]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Ajánlatok, előfizetések létrehozása az Azure Stackben]: ../azure-stack-subscribe-plan-provision-vm.md
  [Alkalmazások elérése az Azure Stackhez]: ../azure-stack-create-service-principals.md
  [* Bérlői azonosító *]: ../azure-stack-identity-overview.md
  [* előfizetési azonosító *]: ../azure-stack-plan-offer-quota-overview.md#subscriptions
  [* az Azure Stack resource manager endpoint *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [API-profilok összefoglalása]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [A virtuális gép, virtuális hálózatok közötti, erőforráscsoportok és storage-fiókot a tesztelő projektet]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Az Azure PowerShell használatával egy egyszerű szolgáltatás létrehozása tanúsítvánnyal]: ../azure-stack-create-service-principals.md
  [A tesztelés-Explorerrel futtatni az egységteszteket.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
