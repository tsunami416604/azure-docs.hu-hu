---
title: API verziója profilokkal a Ruby Azure verem |} Microsoft Docs
description: Ismerje meg az API verzió profilokkal a Ruby Azure verem.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 98d285bf8ec92e7b6baf709d4796ce2f4420c4f2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Ruby Azure verem API-verzió profilok használata

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

## <a name="ruby-and-api-version-profiles"></a>Ruby és API-verzió profilok

A Ruby SDK az Azure verem erőforrás-kezelőhöz biztosít eszközök segítségével felépítéséhez és az infrastruktúra kezelését. Erőforrás-szolgáltató az SDK tartalmazza a számítási, a virtuális hálózatok és a tárolási Ruby nyelveket. A Ruby SDK API-profilok hibrid felhőalapú fejlesztési engedélyezéséhez gondoskodik a globális Azure-erőforrások és az Azure-veremben erőforrások közötti váltáshoz.

Az API-profilt az erőforrás-szolgáltatók és verziót. Az API-profilok segítségével kombinálhatja a különböző típusú.

 - A szolgáltatás legújabb verzióit használni, hogy a **legújabb** az Azure SDK összesítő gem profiljában.
 - Az Azure-verem kompatibilis szolgáltatások használatához a **V2017_03_09** az Azure SDK összesítő gem profiljában.
 - A szolgáltatás legújabb api-verziót használják, használja a **legújabb** az adott gem profiljában. Például, ha szeretné használni a legújabb api-számítási szolgáltatás verziója önmagában, használja a **legújabb** profiljának a **számítási** gem.
 - Api-verziót egy szolgáltatás számára az adott API-verziók a gem belül definiált használatához.

> [!note] 
> Kombinálhatja a beállítások ugyanabban az alkalmazásban.

## <a name="install-the-azure-ruby-sdk"></a>Az Azure Ruby SDK telepítése

 - Hivatalos utasításokat követve telepítse [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
 - Hivatalos utasításokat követve telepítse [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
    - Telepítés közben válassza **Ruby adja hozzá az ELÉRÉSIÚT-változója**
    - Fejlesztői csomag telepítése Ruby telepítésekor, ha a rendszer kéri.
    - Következő lépésként telepítse bundler a következő parancsot:  
      `Gem install bundler`
 - Nem érhető el, ha előfizetés létrehozása és mentése a későbbi előfizetés-azonosító. Előfizetés létrehozása a utasítások [Itt](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
 - Hozzon létre egy egyszerű szolgáltatást, és mentse az Azonosítót és titkos kulcs. Az Azure-verem szolgáltatásnevet létrehozni az útmutatás [Itt](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals). 
 - Győződjön meg arról az egyszerű szolgáltatásnév rendelkezik közreműködői és a tulajdonosi szerepkört az előfizetés. A szerepkör hozzárendelése egyszerű szolgáltatásnév az útmutatás [Itt](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

## <a name="install-the-rubygem-packages"></a>A rubygem csomagok telepítése

Az azure rubygem csomagok közvetlenül is telepítheti.

````Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
````

Ügyeljen arra, hogy az Azure Resource Manager Ruby SDK jelenleg előzetes verzióban érhető, és valószínűleg felület módosítása megtörje a jövőbeli kiadásokban fog rendelkezni. A alverzió növekvő számának jelentős változásokat is jelezhet.

## <a name="usage-of-the-azuresdk-gem"></a>A azure_sdk gem használata

A gem azure_sdk, a Ruby SDK minden támogatott gems egy összegző. Ez gem áll egy **legújabb** profilt, amely támogatja az összes olyan szolgáltatás legújabb verzióját. A rendszerverzióval ellátott profil okozna **V2017_03_09** profilt, amely Azure verem lett tervezve.

A következő paranccsal telepíthető a azure_sdk összesítő gem:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>Előfeltétel

Ahhoz, hogy Ruby Azure SDK-t az Azure veremnek megfelelő, adja meg a következő értékeket, és utána állítsa be a környezeti változók értékeit. Az utasításokat lásd a táblázat az operációs rendszerhez a környezeti változók beállítása után. 

| Érték | Környezeti változók | Leírás | 
| --- | --- | --- | --- |
| Bérlőazonosító | AZURE_TENANT_ID | Az Azure-verem értékének [bérlői azonosító](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| Ügyfél-azonosító | AZURE_CLIENT_ID | A szolgáltatás egyszerű Alkalmazásazonosító mentett egyszerű szolgáltatás létrehozása az előző szakasz ebben a dokumentumban.  |
| Előfizetés azonosítója | AZURE_SUBSCRIPTION_ID | A [előfizetés-azonosító](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) miként férhetnek hozzá az ajánlatok van Azure-készletben. |
| Titkos ügyfélkulcs | AZURE_CLIENT_SECRET | A szolgáltatás egyszerű alkalmazás titkos kulcs mentésekor egyszerű szolgáltatásnév jött létre. |
| Erőforrás-kezelő végpont | ARM_ENDPOINT | Lásd: [az Azure-verem resource manager endpoin](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Az Azure verem resource manager-végpont

A Microsoft Azure Resource Manager rendszer felügyeleti keretrendszere, amely lehetővé teszi a rendszergazdák számára, hogy a központi telepítése, kezelése és figyelése az Azure-erőforrások. Az Azure Resource Manager csoportként, nem pedig egyesével, egyetlen művelettel kezelik ezeket a feladatokat.

A metaadatok lekérheti a Resource Manager-végpontot. A végpont egy JSON-fájl az Ön kódjának futtatásához szükséges adatokat adja vissza.
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

### <a name="set-environmental-variables"></a>Környezeti változók beállítása

**Microsoft Windows**  
A környezeti változók beállítása Windows parancssori ablakba, használja a következő formátumot:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux és Unix-alapú rendszerekhez**  
A Unix-alapú rendszerek például használhatja a parancsot:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Meglévő API-profilok

A azure_sdk összesítő gem rendelkezik a következő két profil:

1. **V2017_03_09**  
  A profil Azure verem számára. Ezt a profilt a szolgáltatások segítségével lehet a leginkább kompatibilis a Azure-készlet.
2. **legújabb**  
  Profil minden szolgáltatás legújabb verziója áll. A szolgáltatás legújabb verzióját használja.

Azure verem és API-profilokkal kapcsolatos további információkért lásd: a [API összegzés profilok](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Az Azure Ruby SDK API-profil használata

A következő sorokat használatával hozható létre egy profil ügyfél. Ez a paraméter csak akkor szükséges Azure verem vagy más magánfelhőkben. Globális Azure alapértelmezés szerint ezek a beállítások már rendelkezik.

````Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
    ENV['AZURE_TENANT_ID'],
    ENV['AZURE_CLIENT_ID'],
    ENV['AZURE_CLIENT_SECRET'],
    active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
````

A profil ügyfél egyedi erőforrás-szolgáltatók, köztük a számítási, tárolási és hálózati eléréséhez használható.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>Adja meg a AzureStack környezeti beállítás funkciók

Az Azure-verem környezetbe a szolgáltatás egyszerű hitelesítéshez, adja meg a használó végpontok **get_active_directory_settings()**. Ez a módszer a **ARM_Endpoint** környezeti változó, amely a környezeti változók létrehozásakor.

````Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
        error_model = JSON.load(response_content)
        fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
end
````

## <a name="samples-using-api-profiles"></a>A minta API-profilok használatával

Használhatja a GitHub repositoreis megoldások létrehozásához Ruby és Azure verem API profilokkal hivatkozásként található a következő mintákat:

 - [Azure-erőforrások és -erőforráscsoportok kezelése Ruby használatával](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Ruby használó virtuális gépek kezelése](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Egy SSH telepítése engedélyezve van a virtuális gép és a Ruby-sablon](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>A minta erőforrás-kezelő és a csoportok

A minta futtatásához győződjön meg arról, hogy Ruby telepítve van. Ha a Visual Studio Code használ, a Ruby SDK letöltése bővítményként is. 

> [!note]  
> A tárház kaphat a minta a "[kezelése Azure-erőforrások és Ruby tartalmazó erőforráscsoportokat](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)".

1. Klónozza a tárházat.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. A csomag használatával függőségek telepítése.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. Hozzon létre egy Azure szolgáltatás egyszerű PowerShell használatával, és beolvasni a szükséges értékeket. 

  Egy egyszerű szolgáltatás létrehozása, lásd: [a Azure PowerShell szolgáltatás használatával hozzon létre egy egyszerű tanúsítvány](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

  Szükséges értékek a következők:
  - Bérlőazonosító
  - Ügyfél-azonosító
  - Titkos ügyfélkulcs
  - Előfizetés azonosítója
  - Erőforrás-kezelő végpont

  Állítsa be az alábbi környezeti változókat a a szolgáltatás egyszerű létrehozott lekért információk segítségével.

  - exportálja a AZURE_TENANT_ID = {a bérlő azonosítója}
  - exportálja a AZURE_CLIENT_ID = {az ügyfél-azonosító}
  - exportálja a AZURE_CLIENT_SECRET = {az ügyfélkulcs}
  - exportálja a AZURE_SUBSCRIPTION_ID = {az előfizetés-azonosító}
  - exportálja a ARM_ENDPOINT = {a AzureStack Resource manager URL-cím}

  > [!note]  
  > A Windows válasszon exportálása helyett.

4. Gondoskodjon arról, hogy a hely változó értéke a AzureStack helyre. Például helyi = "local"

5. Adja hozzá a következő kódsorban, ha még Azure verem vagy más magánfelhőkben használ, amelyekre a jogot az active directory-végpontok.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. A beállítások változó belül adja hozzá az active directory-beállítások és az alap URL-cím Azure verem használható. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Profil ügyfél, amelynek célpontja a Azure verem profil létrehozása:

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. A szolgáltatás egyszerű Azure verem hitelesítést végezni, a végpontok használatával kell meghatározni **get_active_directory_settings()**. Ez a módszer a **ARM_Endpoint** környezeti változó, amely a környezeti változók létrehozásakor.

  ````Ruby  
  def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
      error_model = JSON.load(response_content)
      fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
  end
  ````

9. Futtassa a mintát.

  ````Ruby
    bundle exec ruby example.rb
  ````

## 

## <a name="next-steps"></a>További lépések

* [A PowerShell telepítése az Azure Stack szolgáltatáshoz](azure-stack-powershell-install.md)
* [Az Azure-verem felhasználói PowerShell környezet konfigurálása](azure-stack-powershell-configure-user.md)  
