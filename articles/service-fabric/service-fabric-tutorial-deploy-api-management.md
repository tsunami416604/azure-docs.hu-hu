---
title: "Az Azure Service Fabric integrálható az API Management |} Microsoft Docs"
description: "Gyors bevezetés az Azure API Management és a Service Fabric útmutató."
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
ms.date: 11/10/2017
ms.author: ryanwi
ms.openlocfilehash: da1f2c3170aba9dc13d77a8729a98e7b655edea8
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>A Service Fabric az API Management központi telepítését
Ez az oktatóanyag négy egy sorozat része.  Azure API Management Service Fabric a telepítése a Speciális lehetőséget.  Az API Management akkor hasznos, ha a Service Fabric háttérszolgáltatások vonatkozó útválasztási szabályokat széles skáláját az API-k közzé kell tenni. A felhőalapú alkalmazásokhoz általában adjon meg egy olyan hibaérzékeny pontot érkező felhasználók, eszközök és más alkalmazások előtér-átjáró szükséges. A Service Fabric átjáró lehet bármely állapotmentes szolgáltatások, például egy APP.NET Core alkalmazás, az Event Hubs, IoT-központ vagy Azure API Management forgalom érkező készült. 

Az oktatóanyag bemutatja, hogyan állíthat be [Azure API Management](../api-management/api-management-key-concepts.md) a Service Fabric egy háttér-szolgáltatás a Service Fabric forgalom.  Amikor végzett, egy VNETET az API Management telepítve rendelkezik, konfigurált adatforgalom küldését az állapot nélküli háttérszolgáltatások egy API-művelet. A Service Fabric Azure API Management-forgatókönyvekkel kapcsolatos további tudnivalókért tekintse meg a [áttekintése](service-fabric-api-management-overview.md) cikk.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az API Management telepítése
> * API-kezelés konfigurálása
> * Hozzon létre egy API-művelet
> * Háttér-házirend konfigurálása
> * Adja hozzá az API-t egy termékre

Az oktatóanyag adatsorozat elsajátíthatja, hogyan:
> [!div class="checklist"]
> * Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) Azure-sablon használatával
> * [Bejövő vagy kimenő fürt méretezése](/service-fabric-tutorial-scale-cluster.md)
> * [A futtatókörnyezet egy fürt frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * A Service Fabric az API Management központi telepítését

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Telepítse a [Azure Powershell 4.1-es vagy újabb verziója](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) vagy [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) az Azure-on
- Ha telepít egy Windows-fürt, a Windows környezet beállítása. Telepítés [Visual Studio 2017](http://www.visualstudio.com) és a **Azure fejlesztési**, **ASP.NET és a webes fejlesztési**, és **.NET Core platformfüggetlen fejlesztésekhez**munkaterhelések.  Hozzon létre egy [.NET fejlesztőkörnyezet](service-fabric-get-started.md).
- Ha a Linux-fürt központi telepítése, állítsa be a Java-fejlesztőkörnyezet a [Linux](service-fabric-get-started-linux.md) vagy [MacOS](service-fabric-get-started-mac.md).  Telepítse a [háló CLI szolgáltatás](service-fabric-cli.md). 

## <a name="network-topology"></a>Hálózati topológia
Most, hogy a biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) Azure, telepítse az API Management a virtuális hálózathoz (VNET) alhálózat és az API Management kijelölt NSG. Ebben az oktatóanyagban az API Management Resource Manager-sablon a virtuális Hálózatot, alhálózatot és előző beállított NSG-neveket használja az előre konfigurálva [Windows fürt oktatóanyag](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux fürt oktatóanyag](service-fabric-tutorial-create-vnet-and-linux-cluster.md). Ez az oktatóanyag az Azure-ba, amelyben az API Management és a Service Fabric is ugyanazt a virtuális hálózatot az alhálózatok központilag telepíti a következő topológia:

 ![Képfelirat][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Jelentkezzen be az Azure-ba, és jelölje ki az előfizetését
Az Azure-fiókjába történő bejelentkezéshez jelölje ki az előfizetését, Azure parancsok végrehajtása előtt.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>A Service Fabric háttér-szolgáltatás telepítése

Mielőtt konfigurálná a API Management forgalom irányítására a Service Fabric háttér-szolgáltatáshoz, először kell kérelmek fogadásához egy futó szolgáltatással.  Ha korábban létrehozott egy [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md), .NET Service Fabric-szolgáltatás telepítése.  Ha korábban létrehozott egy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md), Java Service Fabric-szolgáltatás telepítése.

### <a name="deploy-a-net-service-fabric-service"></a>A .NET Service Fabric-szolgáltatás telepítése

Ebben az oktatóanyagban alapvető állapotmentes ASP.NET Core megbízható szolgáltatás létrehozása az alapértelmezett webes API projektet sablon használatával. Ezzel létrehoz egy HTTP-végpont a szolgáltatáshoz, amely Azure API Management keresztül teszi ki.

Indítsa el a Visual Studio rendszergazdaként, és az ASP.NET Core szolgáltatás létrehozása:

 1. A Visual Studio alkalmazásban válassza ki a fájl -> Új projekt.
 2. Válassza ki a Cloud Service Fabric-alkalmazás sablont, és adjon neki nevet **"ApiApplication"**.
 3. Válassza ki az ASP.NET Core szolgáltatás sablont, és a nevet a projektnek **"WebApiService"**.
 4. Válassza a webes API-t az ASP.NET Core 1.1 projekt sablont.
 5. A projekt létrehozása után nyissa meg a `PackageRoot\ServiceManifest.xml` , és távolítsa el a `Port` attribútumot a végpont erőforrás-konfiguráció:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    A port eltávolításával lehetővé teszi, hogy a Service Fabric egy dinamikusan a az alkalmazás porttartományát, a hálózati biztonsági csoport révén a fürt Resource Manager sablon keresztülhaladó forgalmat felé haladjanak API Management megnyitva port megadásához.
 
 6. Nyomja le az F5 ellenőrzése a webes API-t a Visual Studio helyileg nem érhető el. 

    Nyissa meg Service Fabric Explorer és megtekintheti az egyes egy adott példányt az ASP.NET Core szolgáltatás alapcímét megjelenítéséhez a szolgáltatás figyeli. Adja hozzá `/api/values` alap történő címet, majd nyissa meg azt a böngészőbe, amely meghívja a ValuesController a webes API-sablonban a Get metódust. A sablon által biztosított alapértelmezett választ, egy JSON-tömb, amely tartalmazza a két karakterláncot adja vissza:

    ```json
    ["value1", "value2"]`
    ```

    Ez az a végpont, amely az Azure API Management keresztül teszi ki.

 7. Végül telepítse az alkalmazást az Azure-ban a fürthöz. A Visual Studióban, kattintson a jobb gombbal a projektet, és válassza ki **közzététel**. Adja meg a fürt végpontja (például `mycluster.southcentralus.cloudapp.azure.com:19000`) az alkalmazás számára az Azure Service Fabric-fürt központi telepítése.

Az ASP.NET Core állapotmentes szolgáltatások nevű `fabric:/ApiApplication/WebApiService` most futnia kell a Service Fabric-fürt az Azure-ban.

### <a name="create-a-java-service-fabric-service"></a>Hozzon létre egy Java Service Fabric-szolgáltatás
Az ebben az oktatóanyagban egy alapszintű webkiszolgálón, amely echók központi telepítése a felhasználó biztonsági üzeneteket. Az echo server mintaalkalmazás tartalmaz egy HTTP-végpont a szolgáltatáshoz, amely Azure API Management keresztül teszi ki.

1. A Java kezdeti Klónozó lépések minták.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started
   ```

2. Szerkesztés *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml*. A végpont frissítéséhez, így a szolgáltatás-as porton 8081.

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. Mentés *ServiceManifest.xml*, majd létre a EchoServer1.0 alkalmazás.

   ```bash
   cd Services/EchoServer/EchoServer1.0/
   gradle
   ```

4. Telepítse az alkalmazást a fürthöz.

   ```bash
   cd Scripts
   sfctl cluster select --endpoint http://mycluster.southcentralus.cloudapp.azure.com:19080
   ./install.sh
   ```

   A Java állapotmentes szolgáltatások nevű `fabric:/EchoServerApplication/EchoServerService` most futnia kell a Service Fabric-fürt az Azure-ban.

5. Nyissa meg egy böngészőt, és írja be a http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage, megtekintheti az "[1.0-s verzió] Hello World!!!" jelenik meg.

## <a name="download-and-understand-the-resource-manager-template"></a>Töltse le és a Resource Manager-sablon ismertetése
Töltse le és mentse a következő Resource Manager sablon és a paraméterek fájlt:
 
- [APIM.JSON][apim-arm]
- [APIM.Parameters.JSON][apim-parameters-arm]

A következő szakaszok ismertetik az erőforrásokat, amelyet a *apim.json* sablont. További információ a hivatkozásokat követve az egyes szakaszokon belül sablon hivatkozás dokumentációját. A konfigurálható paraméterek a *apim.parameters.json* paraméterfájl vannak beállítva, a cikk későbbi részében.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) ismerteti az API Management szolgáltatáspéldány: név, SKU vagy réteg, erőforráscsoport helye, adatait és virtuális hálózat.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates
[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) API Management biztonsági konfigurálja. A szolgáltatás felderítése használ a fürt eléréséhez használt tanúsítványt a Service Fabric-fürt API-kezelés kell hitelesíteni. Ez az oktatóanyag használja ugyanazt a tanúsítványt a korábban megadott létrehozásakor a [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), amely alapértelmezés szerint a fürt eléréséhez használható. 

Ez az oktatóanyag az ügyfél-hitelesítés és a fürt-csomópontok biztonsági ugyanazt a tanúsítványt használja. Külön ügyfél-tanúsítványt használhatja, ha van a Service Fabric-fürt elérésére. Adja meg a **neve**, **jelszó**, és **adatok** (base-64 kódolású karakterlánc), a titkos kulcs fájlját (.pfx) a fürt létrehozásakor megadott tanúsítvány a Service Fabric-fürt.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends
[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) a háttérszolgáltatáshoz való továbbított forgalmat ismerteti. 

A Service Fabric háttérkiszolgálókon a Service Fabric-fürt esetén a háttér helyett egy adott Service Fabric-szolgáltatás. Ez lehetővé teszi egy útvonalat a fürt több szolgáltatás egyetlen házirendet. A **URL-cím** itt mező értéke egy teljesen minősített nevét a fürt összes kérelem átirányított alapértelmezés szerint ha a háttérkiszolgáló házirendben megadott szolgáltatásnév nem szolgáltatás. Használhat egy hamis nevét, például a "fabric: / hamis/szolgáltatás" Ha nem tervezi, hogy a tartalék szolgáltatást. **resourceId** határozza meg a fürt felügyeleti végpontja.  **clientCertificateThumbprint** és **serverCertificateThumbprints** azonosíthatja a fürt hitelesítéséhez használt tanúsítványok.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products
[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) hoz létre egy termék. Az Azure API Management a termék egy vagy több API-k, valamint a memóriahasználati kvóta és a használati feltételeket tartalmaz. Miután közzétette a termék, a fejlesztők a termék előfizetni és a termék API-k használatának megkezdéséhez. 

Adjon meg egy leíró **displayName** és **leírás** termék. A jelen oktatóanyag esetében szükség van egy előfizetésre, de előfizetés rendszergazdája jóvá nem.  A termék **állapot** "közzétett", és látható a előfizetők számára. 

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis
[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) létrehozza az API-k. Az API Management API ügyfélalkalmazások meghívható műveletkészlet jelöli. A műveletek kerülnek, az API-t egy termék hozzáadódik és tehetők közzé. Miután közzétette az API-k, az előfizetett, és a fejlesztők használják.

- **displayName** bármilyen az API-nevet. A jelen oktatóanyag esetében használja a "Service Fabric-alkalmazás".
- **név** az API-t, például a "service-fabric-alkalmazás" egyedinek és leírónak elnevezi. A fejlesztői és publisher portálon megjelenik. 
- **serviceUrl** végrehajtási az API-t a HTTP-szolgáltatás hivatkozik. Az API management erre a címre kérelmeket továbbítja. Service Fabric háttérkiszolgálókon, az az URL-cím érték nem használható. Bármely érték itt helyezhet el. A jelen oktatóanyag esetében például a "http://servicefabric". 
- **elérési út** az alap URL-címet a API management szolgáltatás a rendszer hozzáfűzi. Az alap URL-cím esetében gyakori, minden API-kezelés szolgáltatás példánya által üzemeltetett API. API Management az API-k által az utótag különbözteti meg, és ezért a utótag minden API-hoz. a megadott közzétevő egyedinek kell lennie. 
- **protokollok** határozza meg, mely protokollokkal az API eléréséhez. Ebben az oktatóanyagban listában **http** és **https**.
- **elérési út** utótag van az API-hoz. A jelen oktatóanyag esetében használja a "myapp".

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations
[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) előtt az API Management az API is használható, műveletek hozzá kell adni az API-hoz.  Külső ügyfelek művelet használatával kommunikál a Service Fabric-fürt futtatja az ASP.NET Core állapotmentes szolgáltatások.

Egy előtér-API művelet hozzáadásához töltse ki az értékeket:

- **displayName** és **leírás** írják le a műveletet. A jelen oktatóanyag esetében használja a "Értékek".
- **módszer** meghatározza azt a HTTP-műveletet.  Adja meg a jelen oktatóanyag esetében **beolvasása**.
- **urlTemplate** rendszer hozzáfűzi az alap URL-címet a API, és azonosítja a egyetlen HTTP-műveletre.  A jelen oktatóanyag esetében használja `/api/values` Ha a .NET-háttérrendszer szolgáltatás hozzáadott vagy `getMessage` Ha hozzáadta a Java háttérszolgáltatáshoz.  Alapértelmezés szerint a URL-címet itt megadott URL-címet kap a háttérkiszolgálón Service Fabric-szolgáltatás. Ha azonos URL-címet Itt a szolgáltatás által használt, például a "/ api/értékek", majd a művelet használható további módosítás nélkül. Megadhat egy URL-címe itt, amely eltér a Service Fabric-szolgáltatás háttéralkalmazása által használt URL-címet ebben az esetben meg is kell adnia egy elérési utat módosítsa úgy a művelet házirend később.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies
[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) hoz létre egy háttér-házirendet, amely kötelékek mindent együtt. Ez azért, ahol konfigurálhatja a Service Fabric háttérszolgáltatáshoz rendszer kérést átirányítja. Ezt a házirendet alkalmazhat API-művelet.  További információkért lásd: [házirendek – áttekintés](/azure/api-management/api-management-howto-policies). 

A [háttérkonfiguráció a Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) biztosít a következő kérés útválasztási vezérlők: 
 - A Service Fabric szolgáltatás példány neve, vagy szoftveresen kötött megadásával példány kiválasztása szolgáltatás (például `"fabric:/myapp/myservice"`) vagy a HTTP-kérelem generált (például `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - A Service Fabric particionálási sémát használó partíciós kulcs létrehozása általi megoldása partíció.
 - Állapotalapú szolgáltatások replika kiválasztása.
 - Megoldási újrapróbálkozási feltételeket, amelyek segítségével adhatja meg a feltételeket újra feloldani a helyét, és küldje el újra a kérelmet.

**policyContent** a házirend XML-tartalmának a Json escape-karakterrel megjelölve.  Ebben az oktatóanyagban kérelmek közvetlenül telepített .NET vagy Java állapotmentes szolgáltatások háttér házirend létrehozása. Adja hozzá a `set-backend-service` bejövő házirendek házirend.  "Szolgáltatásnevet" cserélje `fabric:/ApiApplication/WebApiService` , ha korábban telepítették a .NET-háttérrendszer szolgáltatás, vagy `fabric:/EchoServerApplication/EchoServerService` Ha telepítette a Java-szolgáltatás.
    
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

A Service Fabric háttér-házirend attribútumainak teljes készletét, tekintse meg a [API Management háttér-dokumentáció](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)

## <a name="set-parameters-and-deploy-api-management"></a>Paraméterek beállítása és telepítése az API Management
A következő üres paramétereket a töltse ki a *apim.parameters.json* az üzembe helyezéshez. 

|Paraméter|Érték|
|---|---|
|apimInstanceName|ú-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Fejlesztői|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|CertificatePassword|q6D7nN %6ck@6| 
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;Base-64 kódolású karakterlánc&gt;|
|url_path|/ api/értékek|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.Azure.com:19080|
|inbound_policy|&lt;XML-karakterlánc&gt;|

*certificatePassword* és *serviceFabricCertificateThumbprint* meg kell egyeznie a fürt tanúsítványt, a fürt létrehozásakor használt.  

*serviceFabricCertificate* a tanúsítvány van, mint a base-64 kódolású karakterláncot, amely az alábbi parancsfájl használatával hozhatók létre:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

A *inbound_policy*, "szolgáltatásnevet" cserélje `fabric:/ApiApplication/WebApiService` , ha korábban telepítették a .NET-háttérrendszer szolgáltatás, vagy `fabric:/EchoServerApplication/EchoServerService` Ha telepítette a Java-szolgáltatás.

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

A következő parancsfájl használata a Resource Manager sablonnal és paraméterfájlokkal fájlok telepítése az API Management:

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="test-it"></a>Tesztelheti

Most megpróbálhatja egy kérést küld a háttér-szolgáltatás a Service Fabric keresztül közvetlenül az API Management a [Azure-portálon](https://portal.azure.com).

 1. Válassza ki az API Management szolgáltatásban **API**.
 2. Az a **Service Fabric-alkalmazás** létrehozta az előző lépéseket, jelölje be az API a **teszt** fülre, majd a **értékek** műveletet.
 3. Kattintson a **küldése** gombra kattintva a háttérszolgáltatáshoz tesztelési kérelem küldése.  Egy HTTP-válasz hasonlóan kell megjelennie:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A fürtben a fürt erőforrásán felül egyéb Azure-erőforrások is megtalálhatók. A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése.

Jelentkezzen be az Azure-ba, és válassza ki az előfizetés-azonosító, amelynél el szeretné távolítani a fürt.  Miután bejelentkezett az előfizetés-azonosító található a [Azure-portálon](http://portal.azure.com). Törölje az erőforráscsoportot és használó fürt erőforrásait a [Remove-AzureRMResourceGroup parancsmag](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az API Management telepítése
> * API-kezelés konfigurálása
> * Hozzon létre egy API-művelet
> * Háttér-házirend konfigurálása
> * Adja hozzá az API-t egy termékre

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
