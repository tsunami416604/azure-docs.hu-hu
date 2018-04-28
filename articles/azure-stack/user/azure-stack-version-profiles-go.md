---
title: Profiljaival API verziót NYISSA meg az Azure-verem |} Microsoft Docs
description: Ismerje meg, NYISSA meg az Azure-verem API-verzió profilok használatával kapcsolatban.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 5b881c17b6ad1c9a7e46492f8549f563cfd6d796
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Nyissa meg az Azure-verem API-verzió profilok használata

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

## <a name="go-and-version-profiles"></a>Nyissa meg és verzió

Egy profil a különböző szolgáltatásokhoz különböző verzióival különböző típusú kombinációja. A profilt használó segítséget kombinációs között különböző típusú. Profilok biztosít:

 - Az alkalmazás az adott API-verzióra történő zárolja stabilitását.
 - Az alkalmazás Azure verem és a regionális Azure adatközpontjaiban való kompatibilitást.

Nyissa meg SDK profilok alatt érhetők el a profilok / elérési utat, a verziót a **éééé-hh-nn** formátumban. Most, a legújabb Azure verem profil verzió **2017-03-09**. Egy profil importálni egy adott szolgáltatáshoz, szüksége a megfelelő modul importálása a profilt. Ahhoz például, hogy importálja **számítási** a szolgáltatás **2017-03-09** profil:

````go
import "github.com/Azure/azure-sdk-for-go/profi1es/2e17-e3-eg/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Nyissa meg az Azure SDK telepítése

  1. Telepítse a git szoftvert. Útmutatásért lásd: [első lépések – telepíti a Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Telepítse a [programozási nyelv Ugrás](https://golang.org/dl).  
  Az Azure API-profilok Ugrás 1.9 vagy újabb verziója szükséges.
  3. Az Ugrás Azure SDK-val és függőségeinek telepítése a következő bash parancs futtatásával:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>NYISSA meg a SDK

További információ: az Azure Ugrás SDK található:
- Nyissa meg az Azure SDK, [nyissa meg az Azure SDK telepítése](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- Az Azure Ugrás SDK nyilvánosan elérhető a Githubon: [azure-sdk-az-Ugrás](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Nyissa meg-AutoRest függőségek

Az Ugrás SDK attól függ, hogy a többi kérést küld az Azure Resource Manager végpontok Azure Ugrás-AutoRest modulokat. Importálja az Azure Ugrás-AutoRest modul függőségeket kell [Azure Ugrás-AutoRest a Githubon](https://github.com/Azure/go-autorest). A telepítés bash parancsok található a **telepítése** szakasz.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Azure veremben Ugrás SDK-profilok használatával

Nyissa meg kód minta futtatásához Azure veremben:
  1. Azure SDK telepítéséhez nyissa meg és annak függőségeit. Az utasítás lásd az előző szakaszban [Azure SDK telepítéséhez nyissa meg a](#install-azure-sdk-for-go).
  2. A metaadat-információ lekérése a Resource Manager-végpontot. A végpont egy JSON-fájl futtatásához az Ugrás kód szükséges adatokat adja vissza.
  > [!note]  
  > A **ResourceManagerUrl** az Azure verem Development Kit (ASDK) van: `https://management.local.azurestack.external/`  
  > A **ResourceManagerUrl** integrált rendszerekben van: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > A szükséges metaadatok beolvasása: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  A minta JSON-fájlt:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

  3. Nem érhető el, ha előfizetés létrehozása és mentése a későbbi előfizetés-azonosító. Előfizetés létrehozása a további információkért lásd: [ajánlatok-előfizetések létrehozása az Azure-készletben](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Hozzon létre egy egyszerű "Előfizetés" hatókör és **tulajdonos** szerepkör. Mentse a szolgáltatásnevekről Azonosítót és titkos kulcsot. Egy egyszerű Azure verem létrehozásával kapcsolatos további információkért lásd: [egyszerű szolgáltatásnév létrehozása](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). Az Azure-verem környezet be van állítva.
  5. Szolgáltatás modul importálása a kódban Ugrás SDK profilból. Azure verem profil verziószámának **2017-03-09**. Ahhoz például, hogy a hálózati modul importálása **2017-03-09** profiltípus: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. A függvény létrehozása, és az ügyfél hitelesítésére a **új** ügyfél függvény hívásához szükséges. Hozzon létre egy virtuális hálózati ügyfél, a következő kódot használhatja:  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Állítsa be `<baseURI>` számára a **ResourceManagerUrl** két lépésben használt érték.
  Állítsa be `<subscriptionID>` számára a **SubscriptionID** három lépésben mentett érték.
  Jogkivonat létrehozásához lásd: a hitelesítés az alábbi szakasz.  

  7. API-módszer meghívásához az ügyfélprogrammal, amelyet az előző lépésben hozott létre. Ha például egy virtuális hálózat létrehozása az előző lépésben ügyfél használatával: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Átfogó példát virtuális hálózat létrehozása Azure veremben Ugrás SDK-profilok használatával, lásd: [példa](#example).

## <a name="authentication"></a>Hitelesítés

Ahhoz, hogy a Authorizer tulajdonság az Azure Active Directory Ugrás SDK használatával, a Go-AutoRest modulok telepítése. Ezek a modulok lett telepíteni kell az "Ugrás SDK" telepítésének; Ha nem, telepítse a [hitelesítési csomag a Githubon](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Az erőforrás-ügyfél authorizer a Authorizer kell beállítani. Többféle módszerrel lekérni egy Authorizer; a a teljes listáját lásd itt.

Ebben a szakaszban egy közös módja authorizer jogkivonatok Azure veremben ügyfél hitelesítő adatok használatával:

  1. Ha a tulajdonosi szerepkört az előfizetés az egyszerű szolgáltatás nem érhető el, kihagyhatja ezt a lépést. Máskülönben hozzon létre egy egyszerű [utasításokat]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) , és rendelje hozzá az előfizetéséhez hatókörű "owner" szerepet [utasításokat]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). A szolgáltatás egyszerű alkalmazás azonosítója és a titkos kulcs mentése. 

  2. Importálás **adal** Ugrás-AutoRest-csomagot a kódban. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. Hozzon létre egy oauthConfig NewOAuthConfig módszerrel **adal** modul. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  Állítsa be `<activeDirectoryEndpoint>` "loginEndpoint" értékével a tulajdonság a ResourceManagerUrl metaadatok olvassa be az előző szakaszban ebben a dokumentumban.
  Állítsa be `<tenantID>` értéket az Azure-verem bérlő azonosítójával. 

  4. Végezetül hozza létre egy egyszerű szolgáltatás-jogkivonatot adal modulból NewServicePrincipalToken módszer használatával. 

  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/adal" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  ````
  
  Állítsa be `<activeDirectoryResourceID>` "célközönség" értékek egyikére a jelen dokumentum az előző szakaszban letölteni a ResourceManagerUrl metaadatok listáját.  
  Állítsa be `<clientID>` a szolgáltatás egyszerű alkalmazás azonosítója mentett egyszerű szolgáltatás létrehozása az előző szakasz ebben a dokumentumban.  
  Állítsa be `<clientSecret>` a szolgáltatás egyszerű alkalmazás titkos kulcs mentése egyszerű szolgáltatás létrehozása az előző szakasz ebben a dokumentumban.  

## <a name="example"></a>Példa

Ez a szakasz bemutatja egy minta kód nyissa meg a virtuális hálózat létrehozása az Azure veremben. Ugrás SDK teljes példái című [Azure Ugrás SDk minták tárház](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Azure-verem minták érhetők el a hibrid / elérési út a tárház szolgáltatás mappákban.
> [!note]  
> Az ebben a példában a kódra, győződjön meg arról, hogy rendelkezik-e a használt előfizetés **hálózati** tulajdonosaként erőforrás-szolgáltató **regisztrált**. Annak ellenőrzéséhez, hogy, keresse meg az előfizetést a verem Azure-portálon, és kattintson a **erőforrás-szolgáltatók.**

1. Importálja a szükséges csomagokat a kódban. A modul importálása használjon a legújabb elérhető profil Azure veremben. 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. Adja meg a környezeti változókat. Vegye figyelembe, hogy a virtuális hálózat létrehozása kell rendelkeznie egy erőforráscsoportot. 

  ````go
  var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
      subscriptionID = "yourSubscriptionID"
      baseURI = "yourResourceManagerURL"
      resourceGroupName = "existingResourceGroupName"
  )
  ````

3. Most, hogy a környezeti változók definiált, adja hozzá a metódust a hitelesítési jogkivonat létrehozásához **adal** csomag. Az előző szakaszban hitelesítési adatainak megtekintése.
  
  ````go
  //CreateToken creates a service principal token
  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
  }
  ````

4. Adja hozzá a fő metódust. A fő metódus egy tokent először kap az előző lépésben beállított módszer használatával. Egy ügyfél ezután profilból hálózati modul használatával létrehozza. Végül létrehoz egy virtuális hálózatot. 
  
````go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
    "github.com/Azure/go-autorest/autorest"
    "github.com/Azure/go-autorest/autorest/adal"
    "github.com/Azure/go-autorest/autorest/to"
)

var (
    activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
    tenantID = "yourAzureStackTenantID"
    clientID = "yourServicePrincipalApplicationID"
    clientSecret = "yourServicePrincipalSecret"
    activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
    subscriptionID = "yourSubscriptionID"
    baseURI = "yourResourceManagerURL"
    resourceGroupName = "existingResourceGroupName"
)

//CreateToken creates a service principal token
func CreateToken() (adal.OAuthTokenProvider, error) {
    var token adal.OAuthTokenProvider
    oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
    token, err = adal.NewServicePrincipalToken(
        *oauthConfig,
        clientID,
        clientSecret,
        activeDirectoryResourceID)
    return token, err
}

func main() {
    token, _ := CreateToken()
    vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
    vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
    future, _ := vnetClient.CreateOrUpdate(
        context.Background(),
        resourceGroupName,
        "sampleVnetName",
        network.VirtualNetwork{
            Location: to.StringPtr("local"),
            VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                AddressSpace: &network.AddressSpace{
                    AddressPrefixes: &[]string{"10.0.0.0/8"},
                },
                Subnets: &[]network.Subnet{
                    {
                        Name: to.StringPtr("subnetName"),
                        SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                            AddressPrefix: to.StringPtr("10.0.0.0/16"),
                        },
                    },
                },
            },
        })
    err := future.WaitForCompletion(context.Background(), vnetClient.Client)
    if err != nil {
        fmt.Printf(err.Error())
        return
    }
}

````

## <a name="next-steps"></a>További lépések
* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)
* [Az Azure-verem felhasználói PowerShell környezet konfigurálása](azure-stack-powershell-configure-user.md)  
