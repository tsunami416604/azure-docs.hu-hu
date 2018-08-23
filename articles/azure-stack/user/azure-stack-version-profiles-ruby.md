---
title: API-verzióprofilok használatával a Ruby használatával az Azure Stackben |} A Microsoft Docs
description: Ismerje meg az API-verzióprofilok használata az Azure Stackben Ruby használatával.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: a000a54f79e479567168992cdd0786eb9e8b5c32
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42058717"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>API-verzióprofilok használata az Azure Stackben Ruby használatával

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

## <a name="ruby-and-api-version-profiles"></a>Ruby- és API-verzióprofilok

A Ruby SDK az Azure Stack Resource Manager biztosít eszközöket és az infrastruktúra kezelését. Az SDK-t az erőforrás-szolgáltató például számítási, a virtuális hálózatok és a tárolási Ruby nyelven. A Ruby SDK API-profilok engedélyezése a hibrid felhőalapú fejlesztés segít a globális Azure-erőforrások és az Azure Stacken erőforrások közötti váltáshoz.

Egy API-profilt az erőforrás-szolgáltatókat és szolgáltatásverziók kombinációját. Használhatja egy API-profilt egyesítheti a különböző erőforrástípusok.

 - Az összes szolgáltatás legújabb verzióját használni, hogy a **legújabb** az Azure SDK összesítő gem profiljában.
 - Az Azure Stack kompatibilis szolgáltatások használatához a **V2017_03_09** az Azure SDK összesítő gem profiljában.
 - A szolgáltatás legújabb api-verzió használatához az **legújabb** az adott gem profiljában. Például, ha szeretné használni a legújabb api-version számítási szolgáltatás önálló, használja a **legújabb** profiljának a **számítási** gem.
 - Api-verziót egy szolgáltatáshoz az adott API-verziók a gem definiált használatához.

> [!Note]   
> Kombinálhatja a lehetőségek a ugyanazt az alkalmazást.

## <a name="install-the-azure-ruby-sdk"></a>Azure Ruby SDK telepítése

 - Hivatalos utasításokat követve telepítse [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
 - Hivatalos utasításokat követve telepítse [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
    - Válassza ki a telepítése közben **adja hozzá a Ruby használatával PATH változóhoz**
    - Fejlesztői csomag telepítése során Ruby telepítésének, amikor a rendszer kéri.
    - Ezután telepítse a következő paranccsal bundler:  
      `Gem install bundler`
 - Ha nem érhető el, hozzon létre egy előfizetést és az előfizetés-azonosító későbbi felhasználás céljából. Utasítások alapján hozhat létre olyan előfizetést [Itt](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
 - Hozzon létre egy egyszerű szolgáltatást, és mentse a azonosítója és kulcsa. Az Azure stack-beli szolgáltatásnév létrehozása az útmutatás [Itt](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals). 
 - Ellenőrizze, hogy az egyszerű szolgáltatás rendelkezik közreműködői vagy tulajdonosi szerepkört az előfizetésben. Utasítások a szolgáltatásnév szerepkör hozzárendelése [Itt](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

## <a name="install-the-rubygem-packages"></a>A rubygem csomagok telepítése

Az azure rubygem csomag közvetlenül is telepítheti.

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

Ügyeljen arra, hogy az Azure Resource Manager Ruby SDK előzetes verzióként érhető el, és valószínűleg a jövőbeni verziókból használhatatlanná tévő felület módosításokat fog rendelkezni. Alverzió a nagy mennyiségű kompatibilitástörő változásokat utalhat.

## <a name="usage-of-the-azuresdk-gem"></a>A azure_sdk gem használata

A gem, azure_sdk, a Ruby SDK az összes támogatott gems összesített. A gem áll egy **legújabb** profilt, amely támogatja az összes szolgáltatást a legújabb verzióra. Azt mutatja be egy verzióval ellátott profil **V2017_03_09** profilt, amely az Azure Stack épül.

A következő paranccsal telepíthető a azure_sdk összesítő gem:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>Előfeltétel

Annak érdekében, hogy Ruby az Azure SDK használata az Azure Stack használatával, adja meg a következő értékeket, és adja meg az értékeket a környezeti változókat. Az utasításokat lásd a táblázat az operációs rendszerének a a környezeti változók beállítása után. 

| Érték | Környezeti változók | Leírás | 
| --- | --- | --- | --- |
| Bérlőazonosító | AZURE_TENANT_ID | Az Azure Stack értékét [bérlőazonosító](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| Ügyfél-azonosító | AZURE_CLIENT_ID | A szolgáltatás egyszerű alkalmazást azonosító mentése az előző szakaszban Ez a dokumentum az egyszerű szolgáltatás létrehozásakor.  |
| Előfizetés azonosítója | AZURE_SUBSCRIPTION_ID | A [előfizetés-azonosító](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) van, hogy miként férhetnek hozzá az ajánlatok az Azure Stackben. |
| Titkos ügyfélkulcs | AZURE_CLIENT_SECRET | A szolgáltatás egyszerű alkalmazás titkos kulcs mentése egyszerű szolgáltatás létrehozásakor. |
| Resource Manager-végpont | ARM_ENDPOINT | Lásd: [az Azure Stack resource manager endpoin](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Az Azure Stack resource manager-végpont

A Microsoft Azure Resource Manager-keretrendszert, amely lehetővé teszi a rendszergazdák üzembe helyezése, kezelése és monitorozása az Azure-erőforrások számára. Az Azure Resource Manager képes kezelni ezeket a feladatokat, csoportként, nem pedig külön-külön, egyetlen művelettel.

A metaadat-információkat is kérhet a Resource Manager-végpontot. A végpont egy JSON-fájlt a kód futtatásához szükséges adatokat adja vissza.

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

### <a name="set-environmental-variables"></a>Környezeti változók beállítása

**A Microsoft Windows**  
Állítsa be a környezeti változókat, a Windows-parancssort, használja a következő formátumban:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux és Unix-alapú rendszerek**  
Unix-alapú rendszerekben például használhatja a parancsot:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Meglévő API-profilok

A azure_sdk összesítő gem a következő két profillal rendelkezik:

1. **V2017_03_09**  
  Az Azure Stackhez készült profil. Ez a profil szolgáltatások használatával lehet a leginkább kompatibilis az Azure Stack.
2. **legfrissebb**  
  Profil áll az összes szolgáltatás legújabb verzióit. Az összes szolgáltatást a legújabb verziókat használhatja.

Az Azure Stacket és API-profilokkal kapcsolatos további információkért lásd: egy [összegzése az API-profilok](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure Ruby SDK API-profil használata

A következő sorokat használandó profil ügyfél elindítását. Ez a paraméter csak akkor szükséges, az Azure Stack vagy egyéb privát felhők. Globális Azure alapértelmezés szerint ezek a beállítások már rendelkezik.

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

A profil ügyfél egyéni erőforrás-szolgáltatók, például számítási, tárolási és hálózati eléréséhez használható.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>1.2.9-es környezeti beállítás függvények definiálása

Az Azure Stack-környezet, az egyszerű szolgáltatásnév hitelesítése, definiálni kell a végpontok segítségével **get_active_directory_settings()**. Ezt a módszert használja a **ARM_Endpoint** a környezeti változók létrehozásakor beállított környezeti változót.

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

## <a name="samples-using-api-profiles"></a>API-profilok használatával minták

A következő minták GitHub repositoreis található megoldások létrehozása és a Ruby és az Azure Stack API profilok referenciaként is használhatja:

 - [Azure-erőforrások és -erőforráscsoportok kezelése Ruby használatával](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [A Ruby használata virtuális gépek kezelése](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Üzembe helyezése ssh-t használó virtuális gép, a Ruby-sablonnal](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>Mintául szolgáló Resource Manager és a csoportok

A minta futtatásához, győződjön meg arról, hogy telepítette a Rubyt. Ha Visual Studio Code-ot használja, töltse le a Ruby SDK-bővítményként. 

> [!Note]  
> Akkor kaphat a tárházban, a minta "[Azure-erőforrások és -erőforráscsoportok Ruby használatával](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)".

1. A tárház klónozásához.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. Telepítse a csomagot a függőségeket.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. Egy PowerShell-lel az Azure-szolgáltatásnév létrehozása, és kérje le a szükséges értékeket. 

  Egyszerű szolgáltatás létrehozásával kapcsolatos útmutatóért lásd: [tanúsítvánnyal egyszerű szolgáltatás létrehozása az Azure PowerShell használatával](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

  Szükséges értékek a következők:
  - Bérlőazonosító
  - Ügyfél-azonosító
  - Titkos ügyfélkulcs
  - Előfizetés azonosítója
  - Resource Manager-végpont

  Állítsa be az alábbi környezeti változókat a Szolgáltatásnévből létrehozott lekért információk segítségével.

  - Exportálás AZURE_TENANT_ID = {a bérlő azonosítóját}
  - Exportálás AZURE_CLIENT_ID = {az ügyfél-azonosító}
  - Exportálás AZURE_CLIENT_SECRET = {az Ügyfélkód}
  - Exportálás AZURE_SUBSCRIPTION_ID = {az előfizetés azonosítója}
  - Exportálás ARM_ENDPOINT = {az AzureStack Resource manager URL-cím}

  > [!Note]  
  > A Windows használja a set exportálása helyett.

4. Győződjön meg arról, a hely változó értéke az AzureStack helyére. Például helyi = "local"

5. Adja hozzá a következő kódsort a, ha használja az Azure Stack vagy egyéb privát felhők, amelyekre a megfelelő active directory-végpontokhoz.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. A beállítások változó belül adja hozzá az active directory-beállítások és az Azure Stack használatának a kiindulási URL-cím. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Profil ügyfél, amely célozza az Azure Stack-profil létrehozása:

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. Az egyszerű szolgáltatás az Azure Stack használatával hitelesíteni, a végpontok használatával kell meghatározni **get_active_directory_settings()**. Ezt a módszert használja a **ARM_Endpoint** a környezeti változók létrehozásakor beállított környezeti változót.

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
* [Az Azure Stack felhasználói PowerShell-környezet konfigurálása](azure-stack-powershell-configure-user.md)  
