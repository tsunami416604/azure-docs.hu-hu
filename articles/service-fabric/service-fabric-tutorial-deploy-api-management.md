---
title: "Az Azure Service Fabric integrálása az API Managementtel | Microsoft Docs"
description: "Ebből az oktatóanyagból megtudhatja, hogyan kezdheti meg gyorsan az Azure API Management és a Service Fabric használatát."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 430e813b89f3e0004c517ef77f1028e00ebe5404
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="tutorial-deploy-api-management-with-service-fabric"></a>Oktatóanyag: az API Management üzembe helyezése a Service Fabrickel
Ez az oktatóanyag egy sorozat negyedik része.  Az Azure API Management üzembe helyezése a Service Fabrickel speciális forgatókönyv.  Az API Management akkor hasznos, ha a Service Fabric-háttérszolgáltatásokhoz tartozó útválasztási szabályok széles skálájával szeretne API-kat közzétenni. A felhőalapú alkalmazásokhoz általában előtér-átjáró szükséges, amely egyetlen belépési pontként szolgálhat a felhasználók, eszközök és egyéb alkalmazások számára. A Service Fabricben átjáró lehet bármely, bejövő forgalomra tervezett állapotmentes szolgáltatás, például egy ASP.NET Core-alkalmazás, az Event Hubs, az IoT Hub vagy az Azure API Management. 

Ez az oktatóanyag bemutatja, hogyan állíthatja be az [Azure API Managementet](../api-management/api-management-key-concepts.md) a Service Fabrickel, a forgalom a Service Fabric háttérszolgáltatáshoz való irányítása céljából.  Az oktatóanyag végére rendelkezni fog egy virtuális hálózaton üzembe helyezett API Management szolgáltatással, valamint egy API-művelettel, amely a forgalom állapotmentes háttérszolgáltatásokhoz való irányítására lesz konfigurálva. A Service Fabrickel kapcsolatos Azure API Management-forgatókönyvekre vonatkozó további tudnivalókért tekintse meg az [áttekintő](service-fabric-api-management-overview.md) cikket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az API Management telepítése
> * Az API Management konfigurálása
> * API-művelet létrehozása
> * Háttérszabályzat konfigurálása
> * Az API hozzáadása egy termékhez

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) létrehozása az Azure-ban sablon használatával
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * Az API Management üzembe helyezése a Service Fabrickel

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elkezdése előtt:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Telepítse az [Azure PowerShell-modul 4.1-es vagy újabb verzióját](https://docs.microsoft.com/powershell/azure/install-azurerm-ps), vagy az [Azure CLI 2.0-ás verzióját](/cli/azure/install-azure-cli).
- Biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) létrehozása az Azure-ban
- Ha Windows-fürtöt telepít, állítson be egy Windows fejlesztési környezetet. Telepítse a [Visual Studio 2017](http://www.visualstudio.com) szoftvert, valamint az **Azure-fejlesztési**, **ASP.NET- és webes fejlesztési**, továbbá a **.NET Core platformfüggetlen fejlesztési** számítási feladatokat.  Ezután hozzon létre egy [.NET fejlesztési környezet](service-fabric-get-started.md).
- Ha Linux-fürtöt telepít, állítson be Java fejlesztési környezetet [Linux](service-fabric-get-started-linux.md) vagy [MacOS](service-fabric-get-started-mac.md) operációs rendszeren.  Telepítse a [Service Fabric parancssori felületet](service-fabric-cli.md). 

## <a name="network-topology"></a>Hálózati topológia
Most, hogy biztonságos [Windows-fürttel](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürttel](service-fabric-tutorial-create-vnet-and-linux-cluster.md) rendelkezik az Azure-on, telepítse az API Management szolgáltatást a virtuális hálózatra az API Management számára kijelölt alhálózatra és NSG-re. Ebben az oktatóanyagban az API Management Resource Manager-sablon úgy lett előre konfigurálva, hogy a korábban elvégzett [Windows-fürt oktatóanyag](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt oktatóanyag](service-fabric-tutorial-create-vnet-and-linux-cluster.md) keretében létrehozott virtuális hálózat, alhálózat és NSG neveit használja. Ez az oktatóanyag a következő topológiát telepíti az Azure-ba, amelyben az API Management és a Service Fabric ugyanazon virtuális hálózat alhálózatain találhatók:

 ![Képfelirat][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Jelentkezzen be az Azure-ba, és válassza ki az előfizetését
Azure-parancsok végrehajtása előtt jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Service Fabric-háttérszolgáltatás telepítése

Mielőtt az API Management szolgáltatást a forgalom a Service Fabric-háttérszolgáltatáshoz való irányítására konfigurálná, előbb rendelkeznie kell a kérelmek fogadására képes, futó szolgáltatással.  Ha korábban egy [Windows-fürtöt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) hozott létre, telepítsen egy .NET Service Fabric-szolgáltatást.  Ha korábban egy [Linux-fürtöt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) hozott létre, telepítsen egy Java Service Fabric-szolgáltatást.

### <a name="deploy-a-net-service-fabric-service"></a>.NET Service Fabric-szolgáltatás telepítése

Ebben az oktatóanyagban alapvető állapotmentes ASP.NET Core Reliable Service-szolgáltatást hozhat létre az alapértelmezett Web API-projektsablon használatával. Ezzel létrehoz egy HTTP-végpontot a szolgáltatáshoz, amelyet az Azure API Management szolgáltatáson keresztül tehet közzé.

Indítsa el a Visual Studiót rendszergazdaként, és hozzon létre egy ASP.NET Core-szolgáltatást:

 1. A Visual Studio felületén válassza a File (Fájl) -> New Project (Új projekt) lehetőséget.
 2. Válassza ki a Service Fabric-alkalmazássablont a Cloud (Felhő) területen, és adja neki az **"ApiApplication"** nevet.
 3. Válassza ki az ASP.NET Core állapotmentes szolgáltatássablont, és adja a **"WebApiService"** nevet a projektnek.
 4. Válassza ki a Web API ASP.NET Core 2.0 projektsablont.
 5. A projekt létrehozása után nyissa meg a következőt a `PackageRoot\ServiceManifest.xml` fájlt, és távolítsa el a `Port` attribútumot a végponterőforrás-konfigurációból:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    A port eltávolításával lehetővé teszi, hogy a Service Fabric dinamikusan határozzon meg egy portot az alkalmazás porttartományából, a fürt Resource Manager-sablonjában található hálózati biztonsági csoporton keresztül megnyitva azt, és engedélyezze az API Management szolgáltatásból bejövő forgalmat.
 
 6. Nyomja le az F5 gombot a Visual Studióban, hogy ellenőrizhesse a Web API helyi elérhetőségét. 

    Nyissa meg Service Fabric Explorert, és indítson részletes elemzést az ASP.NET Core szolgáltatás egy adott példányán, hogy láthassa a szolgáltatás által figyelt alapszintű címet. Adja hozzá az alapszintű címhez az `/api/values` elemet, majd nyissa meg egy böngészőben, amellyel meghívja a Get metódust a ValuesControllerre vonatkozóan a Web API-sablonban. Ez a sablon által biztosított alapértelmezett választ adja vissza, amely egy két sztringet tartalmazó JSON-tömb:

    ```json
    ["value1", "value2"]`
    ```

    Ez az a végpont, amely az API Managementen keresztül tehető közzé az Azure-ban.

 7. Végül telepítse az alkalmazást a fürtön az Azure-ban. A Visual Studióban kattintson a jobb gombbal az alkalmazásprojektre, és válassza a **Publish** (Közzététel) lehetőséget. Adja meg a fürt végpontját (például `mycluster.southcentralus.cloudapp.azure.com:19000`) az alkalmazás Service Fabric-fürtön való üzembe helyezéséhez az Azure-ban.

Most már futnia kell egy `fabric:/ApiApplication/WebApiService` nevű ASP.NET Core állapotmentes szolgáltatásnak a Service Fabric-fürtön az Azure-ban.

### <a name="create-a-java-service-fabric-service"></a>Java Service Fabric-szolgáltatás létrehozása
Ebben az oktatóanyagban egy alapszintű webkiszolgálót helyezhet üzembe, amely a felhasználó számára ad vissza üzeneteket. Az echo server mintaalkalmazás tartalmaz egy HTTP-végpontot a szolgáltatáshoz, amelyet Ön az Azure API Management szolgáltatáson keresztül tehet közzé.

1. Klónozza a Java első lépéseket ismertető mintáit.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started/reliable-services-actor-sample
   ```

2. Szerkessze a *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml* fájlt. Frissítse úgy a végpontot, hogy a szolgáltatás a 8081-es portot figyelje.

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. Mentse a *ServiceManifest.xml* fájlt, majd hozza létre az EchoServer1.0 alkalmazást.

   ```bash
   cd Services/EchoServer/EchoServer1.0/
   gradle
   ```

4. Helyezze üzembe az alkalmazást a fürtön.

   ```bash
   cd Scripts
   sfctl cluster select --endpoint https://mycluster.southcentralus.cloudapp.azure.com:19080 --pem <full_path_to_pem_on_dev_machine> --no-verify
   ./install.sh
   ```

   Most már futnia kell egy `fabric:/EchoServerApplication/EchoServerService` nevű Java állapotmentes szolgáltatásnak a Service Fabric-fürtön az Azure-ban.

5. Nyisson meg egy böngészőt, és írja be a következőt: http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage. Az „[1.0-s verzió] Hello World!!!” szövegnek kell megjelennie.

## <a name="download-and-understand-the-resource-manager-templates"></a>A Resource Manager-sablonok letöltése és megismerése
Töltse le és mentse a következő Resource Manager-sablonokat és paraméterfájlt:
 
- [network-apim.json][network-arm]
- [network-apim.parameters.json][network-parameters-arm]
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

A *network-apim.json* sablon telepít egy új alhálózatot és hálózati biztonsági csoportot a virtuális hálózatra, ahová a Service Fabric-fürt telepítve van.

A következő szakaszok ismertetik az *apim.json* sablon által definiált erőforrásokat. További információért kövesse a sablon referenciadokumentációjára mutató hivatkozásokat az egyes szakaszokon belül. Az *apim.parameters.json* paraméterfájlban definiált konfigurálható paraméterek a cikk későbbi részében lesznek beállítva.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
A [Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) írja le az API Management szolgáltatáspéldányt, beleértve annak nevét, SKU-ját vagy szintjét, az erőforráscsoport helyét, a közzétevő adatait és a virtuális hálózatot.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates
A [Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) az API Management szolgáltatás biztonsági beállításait konfigurálja. Az API Managementnek hitelesítést kell végeznie a Service Fabric-fürttel a szolgáltatásészlelés érdekében egy olyan ügyféltanúsítvány használatával, amely rendelkezik a fürthöz való hozzáféréssel. Ez az oktatóanyag a korábban, a [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor) létrehozásakor megadott tanúsítványt használja, amely alapértelmezés szerint használható a fürthöz való hozzáféréshez. 

Ez az oktatóanyag ugyanezt a tanúsítványt használja az ügyfél-hitelesítéshez és a fürt csomópontok közötti biztonságának fenntartásához. Használhat külön ügyféltanúsítványt, ha van ilyen konfigurálva a Service Fabric-fürthöz való hozzáféréshez. Adja meg a Service Fabric-fürt létrehozásakor megadott fürttanúsítvány a titkos kulcsfájljának (.pfx) **nevét**, **jelszavát** és **adatait** (base-64 kódolású sztring).

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends
A [Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) azt a háttérszolgáltatást írja le, amelyre a forgalom irányul. 

A Service Fabric-háttérrendszerek esetében a Service Fabric-fürt a háttérrendszer egy adott Service Fabric-szolgáltatás helyett. Ez lehetővé teszi, hogy egyetlen szabályzat egynél több szolgáltatáshoz irányíthasson a fürtben. Az **url** mező itt a fürt egy olyan szolgáltatásának teljes szolgáltatásneve, amelyhez alapértelmezés szerint az összes kérelem irányul, ha nincs megadva szolgáltatásnév egy háttérszabályzatban. Használhat hamis szolgáltatásnevet (például „fabric:/hamis/szolgáltatás”) ha nem tervezi tartalék szolgáltatás használatát. A **resourceId** határozza meg a fürt felügyeleti végpontját.  A **clientCertificateThumbprint** és a **serverCertificateThumbprints** határozzák meg a fürttel való hitelesítéshez használt tanúsítványokat.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products
A [Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) egy terméket hoz létre. Az Azure API Management szolgáltatásban a termék egy vagy több API-t, valamint a használati kvótát és a használati feltételeket tartalmazza. Egy termék közzététele után a fejlesztők előfizethetnek a termékre, és megkezdhetik a termék API-jainak használatát. 

Adjon meg egy leíró megjelenített nevet (**displayName**) és leírást (**description**) a termékhez. A jelen oktatóanyag esetében szükség van előfizetésre, de az előfizetés rendszergazda általi jóváhagyására nincs.  A termék **állapota** „közzé van téve”, és látható az előfizetők számára. 

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis
A [Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) API-t hoz létre. Az API Management szolgáltatáson belül az API-k egy, az ügyfélalkalmazások által meghívható műveletkészletet jelölnek. A műveletek hozzáadása után az API hozzá lesz adva a termékhez, és közzé lehet tenni. Miután egy API közzé lett téve, elő lehet rá fizetni, és a fejlesztők használatba vehetik.

- A **displayName** (megjelenített név) bármilyen, az API-t jelölő név lehet. Ehhez az oktatóanyaghoz használja a „Service Fabric App” nevet.
- A **name** (név) egyedi és leíró nevet biztosít az API-nak, például: „service-fabric-app”. A fejlesztői és közzétevői portálon jelenik meg. 
- A **serviceUrl** (szolgáltatás URL-címe) az API-t alkalmazó HTTP-szolgáltatásra hivatkozik. Az API Management erre a címre továbbítja a kérelmeket. A Service Fabric-háttérrendszerek esetében ez az URL-érték nem használható. Itt bármilyen érték megadható. A jelen oktatóanyag esetében legyen például „http://servicefabric”. 
- A **path** (útvonal) értéke az API Management szolgáltatás kiindulási URL-címéhez lesz hozzáfűzve. A kiindulási URL-cím egy API Management-szolgáltatáspéldány által üzemeltetett mindegyik API esetében megegyezik. Az API Management az API-kat az utótag alapján különbözteti meg, ezért az utótagnak egy adott közzétevő minden API-ja esetében egyedinek kell lennie. 
- A **protocols** (protokollok) határozza meg, mely protokollok révén lehet hozzáférni az API-hoz. A jelen oktatóanyag esetében a **http** és **https** protokollokat adja meg.
- A **path** (útvonal) az API utótagja. A jelen oktatóanyag esetében legyen „myapp”.

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations
[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) Mielőtt az API Management egy API-ját használni lehetne, műveleteket kell hozzáadni az API-hoz.  A külső ügyfelek egy művelet használatával kommunikálhatnak a Service Fabric-fürtben futó ASP.NET Core állapotmentes szolgáltatással.

Egy előtér-API művelet hozzáadásához töltse ki a következő értékeket:

- A **displayName** (megjelenített név) és a **description** (leírás) írják le a műveletet. A jelen oktatóanyag esetében mindkettő legyen „Values”.
- A **method** (metódus) határozza meg a HTTP-műveletet.  A jelen oktatóanyag esetében a **GET** műveletet adja meg.
- A **urlTemplate** (URL-sablon) értéke az API kiindulási URL-címéhez lesz hozzáfűzve, és egyetlen HTTP-műveletet határoz meg.  A jelen oktatóanyag esetében használja az `/api/values` értéket, ha a .NET-háttérszolgáltatást adta hozzá, vagy a `getMessage` értéket, ha a Java-háttérszolgáltatást.  Alapértelmezés szerint az itt megadott URL-útvonal a Service Fabric-háttérszolgáltatásnak küldött URL-címnek felel meg. Ha ugyanazt az URL-címet használja itt, amelyet a szolgáltatása is használ – például „/api/values” –, a művelet további módosítások nélkül is működni fog. Megadhat a Service Fabric-háttérszolgáltatás által használt URL-címtől eltérő URL-címet is, amely esetben a művelet szabályzatában később útvonal-újraírást is meg kell adnia.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies
A [Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) egy háttérszabályzatot hoz létre, amely mindent összeköt egymással. Itt konfigurálhatja a Service Fabric-háttérszolgáltatást, amelyhez a rendszer a kérelmeket fogja irányítani. Ezt a házirendet bármely API-műveletre alkalmazhatja.  További információkért lásd: [Szabályzatok áttekintése](/azure/api-management/api-management-howto-policies). 

A [Service Fabric háttérrendszerének konfigurációja](/azure/api-management/api-management-transformation-policies#SetBackendService) a következő vezérlőket biztosítja a kérelmek útválasztásához: 
 - Szolgáltatáspéldány kiválasztása szoftveresen kötött (például `"fabric:/myapp/myservice"`) vagy a HTTP-kérelem alapján létrehozott (például `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`) Service Fabric szolgáltatáspéldány-név megadásával.
 - Partíciófeloldás bármely Service Fabric particionálási séma használatával történő partíciókulcs-létrehozással.
 - Replika kiválasztása állapotalapú szolgáltatásokhoz.
 - Feloldási újrapróbálkozási feltételek, amelyek lehetővé teszik a szolgáltatáshelyek ismételt feloldása és a kérelmek újraküldése feltételeinek megszabását.

A **policyContent** a szabályzat Json-feloldójelekkel ellátott XML-tartalma.  A jelen oktatóanyaghoz a kérelmeket a korábban üzembe helyezett .NET vagy Java állapotmentes szolgáltatáshoz irányító háttérszabályzatot hozzon létre. Adjon hozzá egy `set-backend-service` szabályzatot a bejövő szabályzatok alatt.  Az *sf-service-instance-name* értékét cserélje a `fabric:/ApiApplication/WebApiService` értékre, ha korábban a .NET-háttérszolgáltatást helyezte üzembe, vagy a `fabric:/EchoServerApplication/EchoServerService` értékre, ha a Java-szolgáltatást helyezte üzembe.  A *backend-id* egy háttérerőforrásra hivatkozik, ebben az esetben a `Microsoft.ApiManagement/service/backends` erőforrásra, amely az *apim.json* sablonban van definiálva. A *backend-id* más, API Management API-k használatával létrehozott háttérerőforrásra is hivatkozhat. A jelen oktatóanyag esetében a *backend-id* értékét állítsa a *service_fabric_backend_name* paraméter értékére.
    
```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

A Service Fabric-háttérszabályzat attribútumainak teljes listája az [API Management-háttérrendszer dokumentációban](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) található.

## <a name="set-parameters-and-deploy-api-management"></a>Paraméterek beállítása és az API Management telepítése
Töltse ki az *apim.parameters.json* fájl következő üres paramétereit az üzembe helyezéshez. 

|Paraméter|Érték|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Fejlesztői|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6| 
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;base-64 kódolású sztring&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.azure.com:19080|
|inbound_policy|&lt;XML-sztring&gt;|

A *certificatePassword* és a *serviceFabricCertificateThumbprint* értékeinek meg kell felelnie a fürt létrehozásakor használt fürttanúsítványnak.  

A *serviceFabricCertificate* a tanúsítvány base-64 kódolású sztring formájában, amely a következő szkript használatával hozható létre:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

Az *inbound_policy* esetében cserélje le az *sf-service-instance-name* értékét a `fabric:/ApiApplication/WebApiService` értékre, ha korábban a .NET-háttérszolgáltatást helyezte üzembe, vagy a `fabric:/EchoServerApplication/EchoServerService` értékre, ha a Java-szolgáltatást. A *backend-id* egy háttérerőforrásra hivatkozik, ebben az esetben a `Microsoft.ApiManagement/service/backends` erőforrásra, amely az *apim.json* sablonban van definiálva. A *backend-id* más, API Management API-k használatával létrehozott háttérerőforrásra is hivatkozhat. A jelen oktatóanyag esetében a *backend-id* értékét állítsa a *service_fabric_backend_name* paraméter értékére.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

A Resource Manager-sablon és az API Management paraméterfájljai telepítéséhez használja a következő szkriptet:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="test-it"></a>Tesztelje

Most megpróbálhat küldeni egy kérelmet a Service Fabricben lévő háttérszolgáltatásnak az API Management szolgáltatáson keresztül, közvetlenül az [Azure Portalról](https://portal.azure.com).

 1. Az API Management szolgáltatásban válassza az **API** lehetőséget.
 2. A korábbi lépések során létrehozott **Service Fabric-alkalmazás** API-ban válassza ki a **Test** (Teszt) fület, majd a **Values** műveletet.
 3. Kattintson a **Send** (Küldés) gombra a tesztelési kérelem elküldéséhez a háttérszolgáltatásnak.  Az alábbihoz hasonló HTTP-válasznak kell megjelennie:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT

    
    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A fürtben a fürt erőforrásán felül egyéb Azure-erőforrások is megtalálhatók. A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése.

Jelentkezzen be az Azure-ba, és válassza ki azon előfizetés azonosítóját, amelyikkel el szeretné távolítani a fürtöt.  Az [Azure Portalra](http://portal.azure.com) bejelentkezve keresheti meg az előfizetés azonosítóját. Az erőforráscsoport és a fürt erőforrásai a [Remove-AzureRMResourceGroup parancsmaggal](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) törölhetők.

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az API Management telepítése
> * Az API Management konfigurálása
> * API-művelet létrehozása
> * Háttérszabályzat konfigurálása
> * Az API hozzáadása egy termékhez

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
