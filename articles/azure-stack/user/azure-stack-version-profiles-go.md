---
title: API-verzióprofilok használatával nyissa meg az Azure Stackben |} A Microsoft Docs
description: Ismerje meg az API-verzióprofilok használatával nyissa meg az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 30969dcb7549f4107eb72b54d444a639c35b7ba0
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264782"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>API-verzióprofilok használata nyissa meg az Azure Stackben

*Vonatkozik: Az Azure Stack integrált rendszerek és az Azure Stack fejlesztői készlete*

## <a name="go-and-version-profiles"></a>A go és a verzió profilok

A profil a különböző szolgáltatásokhoz különböző verziójú különböző erőforrástípusok kombinációját. A profilt használó segítségével keverheti a különböző erőforrástípusok között. Profilok biztosítja:

 - Az adott API-verziók zárolja az alkalmazás stabilitását.
 - Az alkalmazás az Azure Stack és a regionális Azure-adatközpontok való kompatibilitást.

A Go SDK érhetők el profilok alapján a profilok / elérési út, a verziójuk a **éééé-hh-nn** formátumban. Most, profil-verzió legújabb Azure Stack **2017-03-09**. Az adott szolgáltatás importálására a profil, a profil a megfelelő modul importálnia kell. Például az importálandó **számítási** a szolgáltatás **2017-03-09** profil:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>A Góhoz készült Azure SDK telepítése

  1. A Git telepítése. Útmutatásért lásd: [első lépések – Git telepítése](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Telepítse a [Go programozási nyelvhez](https://golang.org/dl).  
  Az Azure API-profilok Go 1.9 vagy újabb verziójára lesz szükség.
  3. Telepítse a Go Azure SDK és annak függőségeit az alábbi bash-parancs futtatásával:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>A GO SDK-hoz

További információ az Azure GO SDK címen található:
- Az Azure SDK a Go [Góhoz készült Azure SDK telepítése](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- Az Azure a Go SDK nyilvánosan elérhető a Githubon található [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Go-AutoRest függőségek

A GO SDK attól függ, hogy a REST-kérelmeket küldjön az Azure Resource Manager-végpontok Azure Go-AutoRest modulokat. Importálja az Azure Go-AutoRest modul függőségeket kell [Azure Go-AutoRest a Githubon](https://github.com/Azure/go-autorest). A telepítés a bash-parancsok is megtalálhatja a **telepítése** szakaszban.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>GO SDK profilok használata az Azure Stackben

Go-kódokat a minta futtatása az Azure Stacken:
  1. Telepítse az Azure SDK a Go és annak függőségeit. Lásd: az előző szakasz utasításait követve [Góhoz készült Azure SDK telepítése](#install-azure-sdk-for-go).
  2. A metaadatok információinak lekérése a Resource Manager-végpontot. A végpont egy JSON-fájlt a Go kód futtatásához szükséges adatokat adja vissza.

  > [!Note]  
  > A **ResourceManagerUrl** van az Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/`  
  > A **ResourceManagerUrl** integrált rendszerek van: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > A szükséges metaadatokat lekéréséhez: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  JSON-mintafájlt:

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

  3. Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi felhasználás céljából. Előfizetés létrehozásával kapcsolatos információkért lásd: [ajánlatok, előfizetések létrehozása az Azure Stackben](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Egyszerű szolgáltatás létrehozása az "Előfizetés" hatókör és **tulajdonosa** szerepkör. Mentse a szolgáltatásnevek,-azonosítója és kulcsa. Az Azure stack-beli szolgáltatásnév létrehozása információkért lásd: [egyszerű szolgáltatás létrehozása](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). Az Azure Stack-környezet be van állítva.
  5. Egy modul importálása a kódban a Go SDK profil. Az Azure Stack profil aktuális verziója: **2017-03-09**. Ha például a hálózati modul importálásához **2017-03-09** profil típusa: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. A függvényben létrehozásához, és az ügyfél hitelesítéséhez egy **új** ügyfél függvény hívásához szükséges. Hozzon létre egy virtuális hálózati ügyfél, a következő kódot használhatja:  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Állítsa be `<baseURI>` , a **ResourceManagerUrl** két lépésben használt érték.
  Állítsa be `<subscriptionID>` , a **SubscriptionID** három lépésben mentett érték.
  Token létrehozásához lásd: hitelesítés az alábbi szakaszban.  

  7. API-metódusokat hívhat meg az előző lépésben létrehozott az ügyfél használatával. Ha például egy virtuális hálózat létrehozása az előző lépésből származó ügyfél használatával: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Virtuális hálózat létrehozása az Azure Stacken Go SDK-profillal egy teljes példa: [példa](#example).

## <a name="authentication"></a>Hitelesítés

A hitelesítő tulajdonság lekérdezése a Go SDK-val az Azure Active Directoryból, a Go-AutoRest modulok telepítéséhez. Ezeket a modulokat kell már telepítették a "Go SDK" telepítés; Ha nem, telepítse a [hitelesítési csomag a Githubon](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

A hitelesítő kell beállítani a hitelesítő az erőforrás-ügyfél. Get-hitelesítő; különböző módszer áll rendelkezésre egy teljes listáját lásd itt.

Ez a fejezet ismerteti a gyakori módja tokenekhez hitelesítő az Azure Stacken ügyfél-hitelesítő adatok használatával:

  1. Ha egy egyszerű szolgáltatást az előfizetésben tulajdonosi szerepkörrel rendelkező érhető el, hagyja ki ezt a lépést. Ellenkező esetben az egyszerű szolgáltatás létrehozása [utasításokat]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) , és rendelje hozzá egy "owner" szerepkör hatóköre úgy, hogy az előfizetés [utasításokat]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). Mentse a szolgáltatás egyszerű Alkalmazásazonosítót és titkos kulcsot. 

  2. Importálás **adal** Go-AutoRest csomagot a kódban. 
  
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
   
  Állítsa be `<activeDirectoryEndpoint>` "loginEndpoint" értékéhez tulajdonság ResourceManagerUrl metaadatok alapján olvassa be az előző szakaszban ebben a dokumentumban.
  Állítsa be `<tenantID>` érték, az Azure Stack-bérlő azonosítója. 

  4. Végül hozzon létre egy egyszerű szolgáltatás-jogkivonatot az adal modul NewServicePrincipalToken módszer használatával. 

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
  
  Állítsa be `<activeDirectoryResourceID>` egy "célközönség" értékek listája a ResourceManagerUrl metaadatok olvassa be az előző szakaszban Ez a dokumentum.  
  Állítsa be `<clientID>` a szolgáltatás egyszerű alkalmazást azonosító mentése az előző szakaszban Ez a dokumentum az egyszerű szolgáltatás létrehozásakor.  
  Állítsa be `<clientSecret>` a szolgáltatás egyszerű alkalmazás titkos kulcs mentése az előző szakaszban Ez a dokumentum az egyszerű szolgáltatás létrehozásakor.  

## <a name="example"></a>Példa

Ebben a szakaszban a virtuális hálózat létrehozása az Azure Stacken Go-kódokat mintáját szemlélteti. Go SDK teljes példát talál [Azure Go SDk mintaadattárban](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Azure Stack-minták érhetők el hibrid / szolgáltatásokhoz kapcsolódó mappák a tárház található.

> [!Note]  
> Ebben a példában a kód futtatásához, győződjön meg arról, hogy rendelkezik-e a használt előfizetés **hálózati** állapottal erőforrás-szolgáltató **regisztrált**. Ellenőrzi, hogy, keresse meg az előfizetés az Azure Stack portálon, majd kattintson a **erőforrás-szolgáltatók.**

1. Importálja a szükséges csomagokat a kódban. A modul importálása az Azure Stacken használjon a legújabb elérhető profilt. 
  
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

2. A környezeti változókat határozhat meg. Egy virtuális hálózat létrehozásához szüksége lesz egy erőforráscsoportot. 

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

3. Most, hogy a környezeti változók meghatározta, adjon meg egy metódust a hitelesítési jogkivonat létrehozásához **adal** csomagot. Megtekintheti az előző szakaszban hitelesítési adatait.
  
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

4. Adja hozzá a fő metódust. A fő módszert először kér le egy tokent az előző lépésben meghatározott módszer használatával. Egy ügyfél ezután hálózati modulnak a profil használatával létrehoz. Végül létrehoz egy virtuális hálózatot. 
  
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
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)  
