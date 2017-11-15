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
ms.date: 09/13/2017
ms.author: ryanwi
ms.openlocfilehash: 1238863265a227c18ebef8ac2aaeabd802f877d4
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>A Service Fabric az API Management központi telepítését
Ez az oktatóanyag három egy sorozat része.  Üzembe helyezése az Azure API Management a Service Fabric a Speciális lehetőséget, akkor hasznos, ha az útválasztási szabályokat a Service Fabric háttérszolgáltatások széles skáláját API-k közzé kell tenni. Az oktatóanyag bemutatja, hogyan állíthat be [Azure API Management](../api-management/api-management-key-concepts.md) a Service Fabric egy háttér-szolgáltatás a Service Fabric forgalom.  Amikor végzett, egy VNETET az API Management telepítve rendelkezik, konfigurált adatforgalom küldését az állapot nélküli háttérszolgáltatások egy API-művelet. A Service Fabric Azure API Management-forgatókönyvekkel kapcsolatos további tudnivalókért tekintse meg a [áttekintése](service-fabric-api-management-overview.md) cikk.

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
> * A Service Fabric az API Management központi telepítését

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Telepítse a [Azure Powershell 4.1-es vagy újabb verziója](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) vagy [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Hozzon létre egy biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) az Azure-on
- Ha telepít egy Windows-fürt, a Windows környezet beállítása. Telepítés [Visual Studio 2017](http://www.visualstudio.com) és a **Azure fejlesztési**, **ASP.NET és a webes fejlesztési**, és **.NET Core platformfüggetlen fejlesztésekhez**munkaterhelések.  Hozzon létre egy [.NET fejlesztőkörnyezet](service-fabric-get-started.md).
- Ha a Linux-fürt központi telepítése, állítsa be a Java-fejlesztőkörnyezet a [Linux](service-fabric-get-started-linux.md) vagy [MacOS](service-fabric-get-started-mac.md).  Telepítse a [háló CLI szolgáltatás](service-fabric-cli.md). 

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

## <a name="deploy-api-management"></a>Az API Management telepítése
A felhőalapú alkalmazásokhoz általában adjon meg egy olyan hibaérzékeny pontot érkező felhasználók, eszközök és más alkalmazások előtér-átjáró szükséges. A Service Fabric átjáró lehet bármely állapotmentes szolgáltatások, például egy ASP.NET Core alkalmazás, vagy egy másik szolgáltatás, amely a forgalom érkező, például az Event Hubs, az IoT-központ vagy az Azure API Management. Ez az oktatóanyag megismerkedhet az Azure API Management használatával a Service Fabric-alkalmazások átjáróként. Az API Management közvetlenül integrálható a Service Fabric, lehetővé téve az API-k közzététele az útválasztási szabályokat a Service Fabric háttérszolgáltatások széles skáláját. 

Most, hogy a biztonságos [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md) Azure, telepítse az API Management a virtuális hálózathoz (VNET) alhálózat és az API Management kijelölt NSG. Ebben az oktatóanyagban az API Management Resource Manager-sablon a virtuális Hálózatot, alhálózatot és előző beállított NSG-neveket használja az előre konfigurálva [Windows fürt oktatóanyag](service-fabric-tutorial-create-vnet-and-windows-cluster.md) vagy [Linux fürt oktatóanyag](service-fabric-tutorial-create-vnet-and-linux-cluster.md). 

Ez az oktatóanyag az Azure-ba, amelyben az API Management és a Service Fabric is ugyanazt a virtuális hálózatot az alhálózatok központilag telepíti a következő topológia:

 ![Képfelirat][sf-apim-topology-overview]

Töltse le a következő Resource Manager sablon és a Paraméterek:
 
- [APIM.JSON][apim-arm]
- [APIM.Parameters.JSON][apim-parameters-arm]

Adja meg az üres paraméterei a `apim.parameters.json` az üzembe helyezéshez.

A következő parancsfájl használata a Resource Manager sablonnal és paraméterfájlokkal fájlok telepítése az API Management:

```powershell
$ResourceGroupName = "tutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="tutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="configure-api-management"></a>API-kezelés konfigurálása

Után az API Management és a Service Fabric-fürt vannak telepítve, az API Management biztonsági beállításait és a Service Fabric háttér állíthatja be. Ez lehetővé teszi, hogy a szabályzatban háttér szolgáltatás forgalmat küld a Service Fabric-fürt.

### <a name="configure-api-management-security"></a>API Management biztonságának konfigurálása

A Service Fabric háttér konfigurálásához először API biztonsági beállítások megadásához. A biztonsági beállítások konfigurálása, nyissa meg az API Management szolgáltatás az Azure portálon.

#### <a name="enable-the-api-management-rest-api"></a>Az API Management REST API engedélyezése

Az API Management REST API jelenleg az egyetlen lehetőség a háttér-szolgáltatás konfigurálásához. Az első lépés, hogy engedélyezze az API Management REST API-t és a biztonság.

 1. Válassza ki az API Management szolgáltatásban **felügyeleti API** alatt **biztonsági**.
 2. Ellenőrizze a **engedélyezése API Management REST API** jelölőnégyzetet.
 3. Megjegyzés: a **felügyeleti API URL-címe**, amelyek később állíthatja be a Service Fabric-háttéralkalmazás használatára.
 4. Generate egy **Access Token** kiválasztásával lejárati dátummal és a kulcsot, majd kattintson a **Generate** gombra az oldal alján.
 5. Másolás a **hozzáférési jogkivonat** és mentse azt.  A hozzáférési jogkivonat a következő lépésekben használjuk. Vegye figyelembe, hogy ez különbözik az elsődleges és másodlagos kulcsot.

#### <a name="upload-a-service-fabric-client-certificate"></a>A Service Fabric ügyfél-tanúsítvány feltöltése

A szolgáltatás felderítése használ a fürt eléréséhez használt tanúsítványt a Service Fabric-fürt API-kezelés kell hitelesíteni. Az egyszerűség kedvéért ez az oktatóanyag használja ugyanazt a tanúsítványt a korábban megadott létrehozásakor a [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) vagy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), amely alapértelmezés szerint a fürt eléréséhez használható.

 1. Válassza ki az API Management szolgáltatásban **ügyféltanúsítványok** alatt **biztonsági**.
 2. Kattintson a **+ Hozzáadás** gombra.
 2. Válassza ki a titkos kulcs fájlját (.pfx) a fürt tanúsítvány, a Service Fabric-fürt létrehozásakor megadott, adjon neki egy nevet, és adja meg a titkos kulcs jelszava.

> [!NOTE]
> Ez az oktatóanyag az ügyfél-hitelesítés és a fürt-csomópontok biztonsági ugyanazt a tanúsítványt használja. Külön ügyfél-tanúsítványt használhatja, ha van a Service Fabric-fürt elérésére.

### <a name="configure-the-backend"></a>A háttérkiszolgáló beállítása

Most, hogy az API Management a biztonsági beállítások konfigurálása a Service Fabric háttér konfigurálhatja. A Service Fabric háttérkiszolgálókon a Service Fabric-fürt esetén a háttér ahelyett, hogy egy adott Service Fabric-szolgáltatás. Ez lehetővé teszi egy útvonalat a fürt több szolgáltatás egyetlen házirendet.

Ez a lépés szükséges a hozzáférési jogkivonat korábban létrehozott és az ujjlenyomat a fürt tanúsítvány, melyet korábban töltött fel az API Management.

> [!NOTE]
> Az API Management az előző lépésben használt külön ügyfél-tanúsítványt, ha az ügyféltanúsítványt a fürt tanúsítvány ujjlenyomata ebben a lépésben mellett kell az ujjlenyomat.

A következő HTTP PUT kérelmek az API Management API URL-címet korábban feljegyzett, ha engedélyezi az API Management REST API-t konfigurálja a Service Fabric háttérszolgáltatáshoz küldése Megjelenik egy `HTTP 201 Created` választ, ha a parancs sikeres. Minden mező további információkért lásd: az API Management [háttér-API referenciadokumentációt](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend).

HTTP-parancs és az URL-címe:
```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
```

Kérelem fejlécei:
```http
Authorization: SharedAccessSignature <your access token>
Content-Type: application/json
```

A kérelem törzse:
```http
{
    "description": "<description>",
    "url": "<fallback service name>",
    "protocol": "http",
    "resourceId": "<cluster HTTP management endpoint>",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "<cluster HTTP management endpoint>" ],
            "clientCertificateThumbprint": "<client cert thumbprint>",
            "serverCertificateThumbprints": [ "<cluster cert thumbprint>" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

A **URL-cím** itt paramétere a fürt összes kérelem átirányított alapértelmezés szerint ha szolgáltatásnév nem egy háttér-házirendben megadott szolgáltatás egy teljesen minősített nevét. Használhat egy hamis nevét, például a "fabric: / hamis/szolgáltatás" Ha nem tervezi, hogy a tartalék szolgáltatást.

Tekintse meg az API Management [háttér-API referenciadokumentációt](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) kapcsolatban további részleteket a minden mező.

```python
#import requests library for making REST calls
import requests
import json

#specify url
url = 'https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10'

token = "SharedAccessSignature integration&201710140514&Lqnbei7n2Sot6doiNtxMFPUi/m9LsNa+1ZK/PdxqFl49JFWjXh1wW5Gd99r/tIOeHp6dU8UV5iZUdOPfcrm5hg=="

payload = {
    "description": "My Service Fabric backend",
    "url": "fabric:/ApiApplication/ApiWebService",
    "protocol": "http",
    "resourceId": "https://tutorialcluster.eastus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": [ "https://tutorialcluster.eastus.cloudapp.azure.com:19080" ],
            "clientCertificateThumbprint": "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80",
            "serverCertificateThumbprints": [ "97EDD7E4979FB072AF3E480A5E5EE34B1B89DD80" ],
            "maxPartitionResolutionRetries" : 5
        }
    }
}

headers = {'Authorization': token, "Content-Type": "application/json"}

#Call REST API
response = requests.put(url, data=json.dumps(payload), headers=headers)

#Print Response
print(response.status_code)
print(response.text)
```

## <a name="deploy-a-service-fabric-back-end-service"></a>A Service Fabric háttér-szolgáltatás telepítése

Most, hogy a Service Fabric egy háttéralkalmazását az API Management konfigurálva, az API-k esetében, amelyek forgalmat küldeni a Service Fabric-szolgáltatásokhoz hozhat létre a háttér-házirendeket. De először meg kell a Service Fabric-kérelmek fogadásához futó szolgáltatásban.  Ha korábban létrehozott egy [Windows-fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md), .NET Service Fabric-szolgáltatás telepítése.  Ha korábban létrehozott egy [Linux-fürt](service-fabric-tutorial-create-vnet-and-linux-cluster.md), Java Service Fabric-szolgáltatás telepítése.

### <a name="deploy-a-net-service-fabric-service"></a>A .NET Service Fabric-szolgáltatás telepítése

Ebben az oktatóanyagban létrehozhatunk egy alapszintű állapotmentes ASP.NET Core megbízható szolgáltatás az alapértelmezett webes API projektet sablon használatával. Ezzel létrehoz egy HTTP-végpont a szolgáltatáshoz, amely Azure API Management keresztül teszi ki:

```
/api/values
```

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

    Ez lehetővé teszi, hogy a Service Fabric az alkalmazás porttartományát, amelyet azt a hálózati biztonsági csoport révén a fürt Resource Manager sablon keresztülhaladó forgalmat felé haladjanak API Management megnyitni a dinamikus port megadásához.
 
 6. Nyomja le az F5 ellenőrzése a webes API-t a Visual Studio helyileg nem érhető el. 

    Nyissa meg Service Fabric Explorer és megtekintheti az egyes egy adott példányt az ASP.NET Core szolgáltatás alapcímét megjelenítéséhez a szolgáltatás figyeli. Adja hozzá `/api/values` alap történő címet, majd nyissa meg a böngészőben. Ez elindítja a ValuesController a webes API-sablonban a Get metódust. A sablon által biztosított alapértelmezett választ, egy JSON-tömb, amely tartalmazza a két karakterláncot adja vissza:

    ```json
    ["value1", "value2"]`
    ```

    Ez az a végpont, amely az Azure API Management keresztül teszi ki.

 7. Végül telepítse az alkalmazást az Azure-ban a fürthöz. A Visual Studióban, kattintson a jobb gombbal a projektet, és válassza ki **közzététel**. Adja meg a fürt végpontja (például `mycluster.southcentralus.cloudapp.azure.com:19000`) az alkalmazás számára az Azure Service Fabric-fürt központi telepítése.

Az ASP.NET Core állapotmentes szolgáltatások nevű `fabric:/ApiApplication/WebApiService` most futnia kell a Service Fabric-fürt az Azure-ban.

### <a name="create-a-java-service-fabric-service"></a>Hozzon létre egy Java Service Fabric-szolgáltatás
Ebben az oktatóanyagban egy alapszintű webkiszolgálón, amelyen a felhasználók üzenetek echók azt telepíteni. Az echo server mintaalkalmazás tartalmaz egy HTTP-végpont a szolgáltatáshoz, amely Azure API Management keresztül teszi ki.

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


## <a name="create-an-api-operation"></a>Hozzon létre egy API-művelet

Most még az API Management külső ügyfelek által használt szolgáltatással való kommunikációra az ASP.NET Core állapotmentes a Service Fabric-fürt fut egy művelet létrehozására alkalmas.

1. Jelentkezzen be az Azure-portálon, és keresse meg az API Management szolgáltatás központi telepítése.
2. Az API Management szolgáltatás panelen válassza ki a **API-k**
3. Egy olyan új API hozzáadása gombra kattintva **+ API**, majd **üres API** be, és a párbeszédpanel kitöltése:

    - **Webszolgáltatás URL-címe**: A Service Fabric háttérkiszolgálókon, ez az URL-cím érték nem használható. Bármely érték itt helyezhet el. A jelen oktatóanyag esetében használja: "http://servicefabric".
    - **Megjelenítendő név**: bármely nevezze el az API-t. A jelen oktatóanyag esetében használja a "Service Fabric-alkalmazás".
    - **Név**: Adja meg a "service-fabric-alkalmazás".
    - **URL-séma**: válassza **HTTP**, **HTTPS**, vagy **mindkét**. A jelen oktatóanyag esetében használja **mindkét**.
    - **API URL-cím utótag**: Adjon meg egy utótagot az API felületen. A jelen oktatóanyag esetében használja a "myapp".
 
4. Válassza ki **Service Fabric-alkalmazás** API-t, és kattintson a listájáról **+ Hozzáadás művelet** hozzáadása egy előtér-API-művelet. Töltse ki az értékeket:
    
    - **URL-cím**: válasszon **beolvasása** , és adjon meg egy URL-címet a API-t. A jelen oktatóanyag esetében használja a "/ api/értékek".  Alapértelmezés szerint a URL-címet itt megadott URL-címet kap a háttérkiszolgálón Service Fabric-szolgáltatás. Ha azonos URL-címet Itt a szolgáltatás által használt használja, ebben az esetben "/ api/értékek", akkor a művelet működik további módosítás nélkül. Megadhat egy URL-címe itt, amely eltér a Service Fabric-szolgáltatás háttéralkalmazása által használt URL-címet ebben az esetben meg is kell adnia egy elérési utat módosítsa úgy a művelet házirend később.
    - **Megjelenített név**: bármely nevezze el az API-t. A jelen oktatóanyag esetében használja a "Értékek".

5. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-a-backend-policy"></a>Háttér-házirend konfigurálása

A háttérrendszer házirend kötelékek mindent együtt. Ez azért, ahol konfigurálhatja a Service Fabric háttérszolgáltatáshoz rendszer kérést átirányítja. Ezt a házirendet alkalmazhat API-művelet. A [háttérkonfiguráció a Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) biztosít a következő kérés útválasztási vezérlők: 
 - A Service Fabric szolgáltatás példány neve, vagy szoftveresen kötött megadásával példány kiválasztása szolgáltatás (például `"fabric:/myapp/myservice"`) vagy a HTTP-kérelem generált (például `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - A Service Fabric particionálási sémát használó partíciós kulcs létrehozása általi megoldása partíció.
 - Állapotalapú szolgáltatások replika kiválasztása.
 - Megoldási újrapróbálkozási feltételeket, amelyek segítségével adhatja meg a feltételeket újra feloldani a helyét, és küldje el újra a kérelmet.

Ebben az oktatóanyagban a szabályzatban háttér közvetlenül kérelmeket továbbítja a korábban telepített ASP.NET Core állapotmentes szolgáltatások:

 1. Válassza ki, és szerkesztheti a **házirendek bejövő** ehhez kattintson a Szerkesztés ikonra, és jelölje be az értékeket művelet **kódnézetben**.
 2. A kód házirendszerkesztő, adja hozzá a `set-backend-service` házirend a bejövő házirendek, ahogy az itt látható, és kattintson a **mentése** gombra:
    
    ```xml
    <policies>
      <inbound>
        <base/>
        <set-backend-service 
           backend-id="servicefabric"
           sf-service-instance-name="fabric:/ApiApplication/WebApiService"
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

### <a name="add-the-api-to-a-product"></a>Adja hozzá az API-t egy termékre 

Az API hívása előtt azt hozzá kell adni egy termékre, ahol hozzáférést biztosíthat a felhasználóknak. 

 1. Válassza ki az API Management szolgáltatásban **termékek**.
 2. Alapértelmezés szerint az API Management szolgáltatók két termékek: alapszintű és a korlátlan. Jelölje ki a korlátlan terméket.
 3. Válassza ki **+ Hozzáadás API-k**.
 4. Válassza ki a `Service Fabric App` a fenti lépéseket, majd kattintson a létrehozott API a **válasszon** gombra.

### <a name="test-it"></a>Tesztelheti

Most megpróbálhatja egy kérést küld a háttér-szolgáltatás a Service Fabric API Management keresztül közvetlenül az Azure portálról.

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
$ResourceGroupName = "tutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="tutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="conclusion"></a>Összegzés
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
