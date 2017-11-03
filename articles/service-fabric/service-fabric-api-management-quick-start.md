---
title: "Az API Management – első lépések az Azure Service Fabric |} Microsoft Docs"
description: "Ez az útmutató bemutatja, hogyan gyorsan Ismerkedés az Azure API Management és a Service Fabric."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/01/2017
ms.author: vturecek
ms.openlocfilehash: 2969834713fc7c2f1a2e281a6c988158d803dc45
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-with-azure-api-management-quick-start"></a>A Service Fabric az Azure API Management – első lépések

Ez az útmutató bemutatja, hogyan Azure API Management a Service Fabric beállítása és konfigurálása az első API művelet forgalmat küldeni a Service Fabric háttér-szolgáltatásaihoz. A Service Fabric Azure API Management-forgatókönyvekkel kapcsolatos további tudnivalókért tekintse meg a [áttekintése](service-fabric-api-management-overview.md) cikk. 

## <a name="deploy-api-management-and-service-fabric-to-azure"></a>Az Azure API Management és a Service Fabric telepítése

Az első lépés egy megosztott virtuális hálózatot az Azure API Management és a Service Fabric-fürt központi telepítése. Ez lehetővé teszi az API Management és közvetlenül kommunikáljanak a Service Fabric ezért szolgáltatásészlelés, a szolgáltatás partíció megoldása és a forgalom közvetlenül által a háttérszolgáltatáshoz képes végezni a Service Fabric.

### <a name="topology"></a>topológia

Ez az útmutató a következő topológia telepíti az Azure-ba, amelyben az API Management és a Service Fabric-alhálózataihoz az azonos virtuális hálózatban szerepelnek:

 ![Képfelirat][sf-apim-topology-overview]

Gyorsan és egyszerűen a Resource Manager-sablonok minden központi telepítési lépés áll rendelkezésre:

 - Hálózati topológia:
    - [Network.JSON][network-arm]
    - [Network.Parameters.JSON][network-parameters-arm]
 - Service Fabric-fürt:
    - [cluster.JSON][cluster-arm]
    - [cluster.Parameters.JSON][cluster-parameters-arm]
 - API-kezelés:
    - [APIM.JSON][apim-arm]
    - [APIM.Parameters.JSON][apim-parameters-arm]

### <a name="sign-in-to-azure-and-select-your-subscription"></a>Jelentkezzen be az Azure-ba, és jelölje ki az előfizetését

Ez az útmutató használ [Azure PowerShell][azure-powershell]. Amikor egy új PowerShell-munkamenetet indít el, jelentkezzen be az Azure-fiókjával, és jelölje ki az előfizetését, Azure parancsok végrehajtása előtt.
 
Jelentkezzen be az Azure-fiókjával:

```powershell
Login-AzureRmAccount
```

Jelölje ki az előfizetését:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy új erőforráscsoportot az üzembe helyezéshez. Adjon neki egy nevet és egy helyet.

```powershell
New-AzureRmResourceGroup -Name <my-resource-group> -Location westus
```

### <a name="deploy-the-network-topology"></a>A hálózati topológia központi telepítéséhez

Az első lépés, hogy a hálózati topológia, amely az API Management és a Service Fabric-fürt telepítése beállítása. A [network.json] [ network-arm] Resource Manager-sablon létrehozása egy virtuális hálózatot (VNET) két alhálózat és két hálózati biztonsági csoportok (NSG) van konfigurálva. 

A [network.parameters.json] [ network-parameters-arm] paraméterek fájl tartalmazza az alhálózatok és a telepítendő API Management és a Service Fabric az NSG-ket nevét. Ez az útmutató a paraméterértékek nem kell módosítani. Az API Management és a Service Fabric Resource Manager sablonok használata ezek érték található, ezért ha itt módosításuk, módosítania kell azokat a többi Resource Manager sablon ennek megfelelően. 

 1. Töltse le a következő Resource Manager sablon és a Paraméterek:

    - [Network.JSON][network-arm]
    - [Network.Parameters.JSON][network-parameters-arm]

 2. A következő PowerShell-parancs segítségével a hálózat beállítása a Resource Manager sablonnal és paraméterfájlokkal fájlok telepítése:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
    ```

### <a name="deploy-the-service-fabric-cluster"></a>A Service Fabric-fürt központi telepítése

Ha a hálózati erőforrások végzett központi telepítése, a következő lépés a Service Fabric-fürt központi telepítése a virtuális hálózat, az alhálózat és a Service Fabric-fürt számára kijelölt NSG. Ebben az oktatóanyagban a Service Fabric Resource Manager-sablon a virtuális Hálózatot, alhálózatot és az előző lépésben beállított NSG-neveket használja az előre konfigurálva. 

A Service Fabric-fürt Resource Manager sablon hozzon létre egy biztonságos fürtöt a biztonsági tanúsítvány van konfigurálva. Ezzel a tanúsítvánnyal a fürt a csomópontok kommunikáció biztosításához és a Service Fabric-fürt felhasználói hozzáférésének kezelése. Az API Management ezt a tanúsítványt használja a Service Fabric-szolgáltatás a szolgáltatás felderítése eléréséhez.

Ez a lépés szükséges tanúsítvánnyal rendelkező Key Vault a fürt biztonsági. Key Vault használó biztonságos fürtök beállításával kapcsolatos további információkért lásd: [Ez az útmutató a fürt létrehozása az Azure Resource Manager használatával](service-fabric-cluster-creation-via-arm.md)

> [!NOTE]
> Azure Active Directory-hitelesítés mellett a hozzáférés a fürthöz használt tanúsítvány adhat hozzá. Az Azure Active Directory felhasználói hozzáférése a Service Fabric-fürt kezeléséhez ajánlott módja, de nincs szükség az oktatóanyag elvégzéséhez. Egy tanúsítványra szükség mindkét módszer fürt a csomópontok biztonság és az Azure API Management hitelesítést, amely jelenleg nem támogatja az Azure Active Directoryban a Service Fabric háttérkiszolgáló hitelesítéséhez.

 1. Töltse le a következő Resource Manager sablon és a Paraméterek:
 
    - [cluster.JSON][cluster-arm]
    - [cluster.Parameters.JSON][cluster-parameters-arm]

 2. Adja meg az üres paraméterei a `cluster.parameters.json` fájl az üzembe helyezés többek között a [Key Vault információk](service-fabric-cluster-creation-via-arm.md#set-up-a-key-vault) a fürt tanúsítvány.

 3. A következő PowerShell-parancs segítségével a Resource Manager sablonnal és paraméterfájlokkal fájlokat, hogy a Service Fabric-fürt központi telepítése:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
    ```

### <a name="deploy-api-management"></a>Az API Management telepítése

Végezetül az alhálózat a virtuális hálózat és az API Management kijelölt NSG API Management központi telepítését. Nem kell a Service Fabric fürt üzembe helyezés befejeződik az API Management üzembe helyezése előtt várja meg. 

Ebben az oktatóanyagban az API Management Resource Manager-sablon a virtuális Hálózatot, alhálózatot és az előző lépésben beállított NSG-neveket használja az előre konfigurálva. 

 1. Töltse le a következő Resource Manager sablon és a Paraméterek:
 
    - [APIM.JSON][apim-arm]
    - [APIM.Parameters.JSON][apim-parameters-arm]

 2. Adja meg az üres paraméterei a `apim.parameters.json` az üzembe helyezéshez.

 3. A következő PowerShell-parancs segítségével a Resource Manager sablonnal és paraméterfájlokkal fájlok telepítése az API Management:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <my-resource-group> -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
    ```

## <a name="configure-api-management"></a>API-kezelés konfigurálása

Után az API Management és a Service Fabric-fürt vannak telepítve, a Service Fabric háttér állíthatja be az API Management. Ez lehetővé teszi, hogy a szabályzatban háttér szolgáltatás forgalmat küld a Service Fabric-fürt.

### <a name="api-management-security"></a>API Management biztonsági

A Service Fabric háttér konfigurálásához először API biztonsági beállítások megadásához. A biztonsági beállítások konfigurálása, nyissa meg az API Management szolgáltatás az Azure portálon.

#### <a name="enable-the-api-management-rest-api"></a>Az API Management REST API engedélyezése

Az API Management REST API jelenleg az egyetlen lehetőség a háttér-szolgáltatás konfigurálásához. Az első lépés, hogy engedélyezze az API Management REST API-t és a biztonság.

 1. Válassza ki az API Management szolgáltatásban **felügyeleti API - előzetes** alatt **biztonsági**.
 2. Ellenőrizze a **engedélyezése API Management REST API** jelölőnégyzetet.
 3. Megjegyzés: a felügyeleti API URL-CÍMÉT – Ez az URL-cím később fogjuk használni a Service Fabric-háttérrendszer beállítása
 4. Generate egy **hozzáférési tokent** kiválasztásával lejárati dátummal és a kulcsot, majd kattintson a **Generate** gombra az oldal alján.
 5. Másolás a **hozzáférési jogkivonat** és mentheti – Ez a következő lépésekben fogjuk használni. Vegye figyelembe, hogy ez különbözik az elsődleges és másodlagos kulcsot.

#### <a name="upload-a-service-fabric-client-certificate"></a>A Service Fabric ügyfél-tanúsítvány feltöltése

A szolgáltatás felderítése használ a fürt eléréséhez használt tanúsítványt a Service Fabric-fürt API-kezelés kell hitelesíteni. Az egyszerűség kedvéért ez az oktatóanyag a Service Fabric-fürt, amely alapértelmezés szerint a fürt eléréséhez használható létrehozásakor megadott ugyanazt a tanúsítványt használja.

 1. Válassza ki az API Management szolgáltatásban **ügyféltanúsítványok - előzetes** alatt **biztonsági**.
 2. Kattintson a **+ Hozzáadás** gomb
 2. Válassza ki a titkos kulcs fájlját (.pfx) a fürt tanúsítvány, a Service Fabric-fürt létrehozásakor megadott, adjon neki egy nevet, és adja meg a titkos kulcs jelszava.

> [!NOTE]
> Ez az oktatóanyag az ügyfél-hitelesítés és a fürt-csomópontok biztonsági ugyanazt a tanúsítványt használja. Külön ügyfél-tanúsítványt használhatja, ha van a Service Fabric-fürt elérésére.

### <a name="configure-the-backend"></a>A háttérkiszolgáló beállítása

Most, hogy az API Management a biztonsági beállítások konfigurálása a Service Fabric háttér konfigurálhatja. A Service Fabric háttérkiszolgálókon a Service Fabric-fürt esetén a háttér ahelyett, hogy egy adott Service Fabric-szolgáltatás. Ez lehetővé teszi egy útvonalat a fürt több szolgáltatás egyetlen házirendet.

Ebben a lépésben az előző lépésben az API Management feltöltött a fürt tanúsítványt igényel a hozzáférési jogkivonat korábban létrehozott és az ujjlenyomat.

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

A **URL-cím** itt paraméter értéke a teljes szolgáltatás a szolgáltatás nevét a a fürt összes kérelem átirányított alapértelmezés szerint ha a háttérkiszolgáló házirendben megadott szolgáltatásnév nem. Használhat egy hamis nevét, például a "fabric: / hamis/szolgáltatás" Ha nem tervezi, hogy a tartalék szolgáltatást. Ne feledje, a **URL-cím** formátumban kell megadni "fabric: / alkalmazás/szolgáltatás" akkor is, ha egy hamis tartalék szolgáltatás.

Tekintse meg az API Management [háttér-API referenciadokumentációt](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#a-namebackenda-backend) kapcsolatban további részleteket a minden mező.

#### <a name="example"></a>Példa

```http
PUT https://your-apim.management.azure-api.net/backends/servicefabric?api-version=2016-10-10
Authorization: SharedAccessSignature 230948023984&Ld93cRGcNU6KZ4uVz7JlfTec4eX43Q9Nu8ndatOgBzs6+f559Pkf3iHX2cSge+r42pn35qGY3TitjrIl13hwcQ==
Content-Type: application/json

{
    "description": "My Service Fabric backend",
    "url": "fabric:/myapp/myservice",
    "protocol": "http",
    "resourceId": "https://your-cluster.westus.cloudapp.azure.com:19080",
    "properties": {
        "serviceFabricCluster": {
            "managementEndpoints": ["https://your-cluster.westus.cloudapp.azure.com:19080"],
            "clientCertificateThumbprint": "57bc463aba3aea3a12a18f36f44154f819f0fe32",
            "serverCertificateThumbprints": ["57bc463aba3aea3a12a18f36f44154f819f0fe32"],
            "maxPartitionResolutionRetries" : 5
        }
    }
}
```

## <a name="deploy-a-service-fabric-back-end-service"></a>A Service Fabric háttér-szolgáltatás telepítése

Most, hogy a Service Fabric egy háttéralkalmazását az API Management konfigurálva, az API-k esetében, amelyek forgalmat küldeni a Service Fabric-szolgáltatásokhoz hozhat létre a háttér-házirendeket. De először meg kell a Service Fabric-kérelmek fogadásához futó szolgáltatásban.

### <a name="create-a-service-fabric-service-with-an-http-endpoint"></a>A Service Fabric-szolgáltatás egy HTTP-végpont létrehozása

Ebben az oktatóanyagban létrehozunk egy alapszintű állapotmentes ASP.NET Core megbízható szolgáltatás az alapértelmezett webes API projektet sablon használatával. Ezzel létrehoz egy HTTP-végpont a szolgáltatás, amely akkor lesz Azure API Management elérhetővé:

```
/api/values
```

Első lépésként [állítja be a fejlesztési környezetet az ASP.NET Core fejlesztési](service-fabric-add-a-web-frontend.md#set-up-your-environment-for-aspnet-core).

Miután beállította a fejlesztőkörnyezetet, indítsa el a Visual Studio rendszergazdaként, és az ASP.NET Core szolgáltatás létrehozása:

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

    Ez a végpont fogjuk az Azure API Management keresztül teszi ki.

 7. Végül telepítse az alkalmazást az Azure-ban a fürthöz. [Visual Studio használatával](service-fabric-publish-app-remote-cluster.md#to-publish-an-application-using-the-publish-service-fabric-application-dialog-box), kattintson a jobb gombbal a projektet, és válassza ki **közzététel**. Adja meg a fürt végpontja (például `mycluster.westus.cloudapp.azure.com:19000`) az alkalmazás számára az Azure Service Fabric-fürt központi telepítése.

Az ASP.NET Core állapotmentes szolgáltatások nevű `fabric:/ApiApplication/WebApiService` most futnia kell a Service Fabric-fürt az Azure-ban.

## <a name="create-an-api-operation"></a>Hozzon létre egy API-művelet

Most még az API Management külső ügyfelek által használt szolgáltatással való kommunikációra az ASP.NET Core állapotmentes a Service Fabric-fürt fut egy művelet létrehozására alkalmas.

 1. Jelentkezzen be az Azure-portálon, és keresse meg az API Management szolgáltatás központi telepítése.
 2. Az API Management szolgáltatás panelen válassza ki a **API-k – előzetes**
 3. Egy olyan új API hozzáadni, kattintson a **üres API** mezőbe, majd töltse ki a párbeszédpanelen:

     - **Webszolgáltatás URL-címe**: A Service Fabric háttérkiszolgálókon, ez az URL-cím érték nem használható. Bármely érték itt helyezhet el. A jelen oktatóanyag esetében használja: `http://servicefabric`.
     - **Név**: bármely nevezze el az API-t. A jelen oktatóanyag esetében használja `Service Fabric App`.
     - **URL-séma**: jelölje be a HTTP, HTTPS vagy mindkettőt. A jelen oktatóanyag esetében használja `both`.
     - **API URL-cím utótag**: Adjon meg egy utótagot az API felületen. A jelen oktatóanyag esetében használja `myapp`.
 
 4. Az API létrehozása után kattintson **+ Hozzáadás művelet** hozzáadása egy előtér-API-művelet. Töltse ki az értékeket:
    
     - **URL-cím**: válasszon `GET` , és adjon meg egy URL-címet a API-t. A jelen oktatóanyag esetében használja `/api/values`.
     
       Alapértelmezés szerint a URL-címet itt megadott URL-címet kap a háttérkiszolgálón Service Fabric-szolgáltatás. Ha azonos URL-címet Itt a szolgáltatás által használt, ebben az esetben `/api/values`, majd a művelet további módosítás nélkül működik. Megadhat egy URL-címe itt, amely eltér a Service Fabric-szolgáltatás háttéralkalmazása által használt URL-címet, ebben az esetben is szüksége lesz később adjon meg egy elérési utat módosítsa úgy a művelet házirend.
     - **Megjelenített név**: bármely nevezze el az API-t. A jelen oktatóanyag esetében használja `Values`.

## <a name="configure-a-backend-policy"></a>Háttér-házirend konfigurálása

A háttérrendszer házirend kötelékek mindent együtt. Ez azért, ahol konfigurálhatja a Service Fabric háttérszolgáltatáshoz rendszer kérést átirányítja. Ezt a házirendet alkalmazhat API-művelet. A [háttérkonfiguráció a Service Fabric](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService) biztosít a következő kérés útválasztási vezérlők: 
 - A Service Fabric szolgáltatás példány neve, vagy szoftveresen kötött megadásával példány kiválasztása szolgáltatás (például `"fabric:/myapp/myservice"`) vagy a HTTP-kérelem generált (például `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - A Service Fabric particionálási sémát használó partíciós kulcs létrehozása általi megoldása partíció.
 - Állapotalapú szolgáltatások replika kiválasztása.
 - Megoldási újrapróbálkozási feltételeket, amelyek segítségével adhatja meg a feltételeket újra feloldani a helyét, és küldje el újra a kérelmet.

Ebben az oktatóanyagban a szabályzatban háttér közvetlenül kérelmeket továbbítja a korábban telepített ASP.NET Core állapotmentes szolgáltatások:

 1. Válassza ki, és szerkesztheti a **házirendek bejövő** a a `Values` ehhez kattintson a Szerkesztés ikonra, és jelölje be a művelet **kódnézetben**.
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

### <a name="add-the-api-to-a-product"></a>A termék adja hozzá az API-t. 

Az API hívása előtt azt hozzá kell adni egy termékre, ahol hozzáférést biztosíthat a felhasználóknak. 

 1. Válassza ki az API Management szolgáltatásban **termékek - előzetes**.
 2. Alapértelmezés szerint az API Management szolgáltatók két termékek: alapszintű és a korlátlan. Jelölje ki a korlátlan terméket.
 3. Válassza ki az API-k.
 4. Kattintson a **+ Hozzáadás** gombra.
 5. Válassza ki a `Service Fabric App` a fenti lépéseket, majd kattintson a létrehozott API a **válasszon** gombra.

### <a name="test-it"></a>Tesztelheti

Most megpróbálhatja egy kérést küld a háttér-szolgáltatás a Service Fabric API Management keresztül közvetlenül az Azure portálról.

 1. Válassza ki az API Management szolgáltatásban **API - előzetes**.
 2. Az a `Service Fabric App` létrehozta az előző lépéseket, jelölje be az API a **teszt** fülre.
 3. Kattintson a **küldése** gombra kattintva a háttérszolgáltatáshoz tesztelési kérelem küldése.

## <a name="next-steps"></a>Következő lépések

Ezen a ponton rendelkeznie kell egy egyszerű telepítése a Service Fabric- és API-kezelés.

Ez az oktatóanyag segítségével gyorsan beállíthatja alapvető a tanúsítvány alapú hitelesítést a Service Fabric-fürt használja. A Service Fabric-fürt speciális felhasználóhitelesítést célszerű a [Azure Active Directory hitelesítési](service-fabric-cluster-creation-via-arm.md#set-up-azure-active-directory-for-client-authentication). 

Ezt követően [létrehozása és speciális termékbeállítások konfigurálása az Azure API Management](https://docs.microsoft.com/azure/api-management/api-management-howto-product-with-rules) valós forgalom az alkalmazás előkészítése.

<!-- links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json


<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-api-management-quickstart/sf-apim-topology-overview.png
