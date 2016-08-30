
<properties
   pageTitle="Application Gateway létrehozása Azure Resource Manager-sablonokkal| Microsoft Azure"
   description="Ez az oldal utasításokat tartalmaz egy Azure Application Gateway Azure Resource Manager-sablonnal történő létrehozásához"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="joaoma"/>


# Application Gateway létrehozása az Azure Resource Manager-sablonokkal

Az Azure Application Gateway egy 7. rétegbeli terheléselosztó. Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak. Az Application Gateway az alábbi alkalmazáskézbesítési funkciókkal rendelkezik: HTTP-terheléselosztás, cookie-alapú munkamenet-affinitás és Secure Sockets Layer (SSL) alapú kiszervezés.

> [AZURE.SELECTOR]
- [Klasszikus Azure PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager-sablon](application-gateway-create-gateway-arm-template.md)

<BR>

Ismertetjük, hogy miként tölthet le és módosíthat egy meglévő Azure Resource Manager-sablont a GitHubból, és miként helyezheti üzembe a sablont a GitHubból, PowerShellből és az Azure parancssori felületéről.

Ha közvetlenül a GitHubból helyezi üzembe az Azure Resource Manager-sablont változtatások nélkül, ugorjon a sablont a GitHubból telepítő lépésre.


## Forgatókönyv

Ebben a forgatókönyvben az alábbiakat fogja tenni:

- Létrehoz egy Application Gateway-t két példánnyal.
- Létrehoz egy VirtualNetwork1 nevű virtuális hálózatot a 10.0.0.0/16 egy fenntartott CIDR-blokkjával.
- Létrehoz egy Appgatewaysubnet nevű alhálózatot, amelynek a CIDR-blokkja 10.0.0.0/28 lesz.
- Beállít két korábban konfigurált háttér IP-címet a webkiszolgálóknak, amelyek között el szeretné osztani a forgalom terhelését. Ebben a példasablonban a két háttér IP-cím 10.0.1.10 és 10.0.1.11.

>[AZURE.NOTE] Ezek a sablon paraméterei. A sablon személyre szabásához módosíthatja a szabályokat, a figyelőt és az SSL-t, amely az azuredeploy.json elemet nyitja meg.



![Forgatókönyv](./media/application-gateway-create-gateway-arm-template/scenario-arm.png)



## Az Azure Resource Manager-sablon letöltése és megismerése

A GitHubból letöltheti a meglévő Azure Resource Manager-sablont, amellyel létrehozhat egy virtuális hálózatot két alhálózattal, végrehajthatja a kívánt módosításokat, és újra felhasználhatja azt. Ehhez kövesse az alábbi lépéseket:

1. Navigáljon az [Application Gateway létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-application-gateway-create) szakaszra.
2. Kattintson az **azuredeploy.json**, majd a **RAW** elemre.
3. Mentse a fájlt egy helyi mappába a számítógépén.
4. Ha már ismeri az Azure Resource Manager-sablonokat, akkor ugorjon a 7. lépéshez.
5. Nyissa meg az előbb mentett fájlt, és nézze át az 5. sorban a **paraméterek** alatt látható tartalmakat. Az Azure Resource Manager-sablonparaméterek az üzembe helyezés során kitölthető paraméterek helyőrzőiként működnek.

  	| Paraméter | Leírás |
  	|---|---|
  	| **location** | Az Azure-régió, ahol az Application Gateway létrejön |
  	| **VirtualNetwork1** | Az új virtuális hálózat neve |
  	| **addressPrefix** | A virtuális hálózat címtere CIDR-formátumban |
  	| **ApplicationGatewaysubnet** | Az Application Gateway alhálózatának neve |
  	| **subnetPrefix** | Az Application Gateway alhálózatának CIDR-blokkja |
  	| **skuname** | Az SKU példány mérete |
  	| **capacity** | Példányok száma |
  	| **backendaddress1** | Az első webkiszolgáló IP-címe |
  	| **backendaddress2** | A második webkiszolgáló IP-címe |


>[AZURE.IMPORTANT] A GitHubban fenntartott Azure Resource Manager-sablonok idővel módosulhatnak. Ne feledje el ellenőrizni a sablont, mielőtt használja azt.

6. Ellenőrizze a **resources** alatt látható tartalmat, és figyelje meg a következőket:

    - **type**. A sablon által létrehozott erőforrástípus. Ebben az esetben a típus **Microsoft.Network/applicationGateways**, amely egy Application Gateway-t jelöl.
    - **name**. Az erőforrás neve. Figyelje meg a **[parameters('applicationGatewayName')]** használatát, ami azt jelzi, hogy a nevet a felhasználó vagy egy paraméterfájl adja meg az üzembe helyezés során bemenetként.
    - **properties**. Az erőforrás tulajdonságainak listája. A sablon az Application Gateway létrehozása során a virtuális hálózatot és a nyilvános IP-címet használja.

7. Lépjen vissza a https://github.com/Azure/azure-quickstart-templates/blob/master/101-application-gateway-create/ webhelyre.
8. Kattintson az **azuredeploy-paremeters.json**, majd a **RAW** elemre.
9. Mentse a fájlt egy helyi mappába a számítógépén.
10. Nyissa meg az előbb mentett fájlt, és módosítsa a paraméterek értékeit. Az alábbi értékek használatával helyezze üzembe a forgatókönyvünkben ismertetett Application Gateway-t.

        {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        {
        "location" : {
        "value" : "West US"
        },
        "addressPrefix": {
        "value": "10.0.0.0/16"
        },
        "subnetPrefix": {
        "value": "10.0.0.0/24"
        },
        "skuName": {
        "value": "Standard_Small"
        },
        "capacity": {
        "value": 2
        },
        "backendIpAddress1": {
        "value": "10.0.1.10"
        },
        "backendIpAddress2": {
        "value": "10.0.1.11"
        }
        }

11. Mentse a fájlt. A JSON-sablont és a paramétersablont online JSON érvényesítési eszközök, például a [JSlint.com](http://www.jslint.com/) segítségével tesztelheti.

## Az Azure Resource Manager-sablon üzembe helyezése a PowerShell használatával

Ha még nem használta az Azure PowerShellt, tekintse meg [Az Azure PowerShell telepítése és konfigurálása](../powershell-install-configure.md) című részt, majd kövesse az utasításokat egészen az utolsó lépésig az Azure-ba való bejelentkezéshez és az előfizetése kiválasztásához.

### 1. lépés

        Login-AzureRmAccount



### 2. lépés

Keresse meg a fiókot az előfizetésekben.

        get-AzureRmSubscription

A rendszer kérni fogja a hitelesítő adatokkal történő hitelesítést.<BR>

### 3. lépés

Válassza ki, hogy melyik Azure előfizetést fogja használni. <BR>


        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 4. lépés


Szükség esetén hozzon létre egy új erőforráscsoportot a **New-AzureResourceGroup** parancsmaggal. Az alábbi példában létre fog hozni egy új, AppgatewayRG nevű erőforráscsoportot az USA keleti régiójában.

     New-AzureRmResourceGroup -Name AppgatewayRG -Location "East US"
        VERBOSE: 5:38:49 PM - Created resource group 'AppgatewayRG' in location 'eastus'


        ResourceGroupName : AppgatewayRG
        Location          : eastus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                     Actions  NotActions
                     =======  ==========
                      *

        ResourceId        : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/AppgatewayRG

Futtassa a **New-AzureRmResourceGroupDeployment** parancsmagot, hogy a fent letöltött és módosított sablonnal és paraméterfájlokkal üzembe helyezhesse az új virtuális hálózatot.

        New-AzureRmResourceGroupDeployment -Name TestAppgatewayDeployment -ResourceGroupName AppgatewayRG `
           -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json

A parancssor az alábbi kimenetet fogja létrehozni:

        DeploymentName    : testappgatewaydeployment
        ResourceGroupName : appgatewayRG
        ProvisioningState : Succeeded
        Timestamp         : 9/19/2015 1:49:41 AM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                   Name             Type                       Value
                   ===============  =========================  ==========
                   location         String                     East US
                   addressPrefix    String                     10.0.0.0/16
                   subnetPrefix     String                     10.0.0.0/24
                   skuName          String                     Standard_Small
                   capacity         Int                        2
                   backendIpAddress1  String                     10.0.1.10
                   backendIpAddress2  String                     10.0.1.11

        Outputs           :


## Az Azure Resource Manager-sablon üzembe helyezése az Azure CLI használatával

A letöltött Azure Resource Manager-sablon Azure CLI-vel történő üzembe helyezéséhez kövesse az alábbi lépéseket:

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Az Azure CLI telepítése és konfigurálása](../xplat-cli-install.md) című szakaszt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Az **azure config mode** parancs futtatásával váltson az Erőforrás-kezelő módra, a lent látható módon.

        azure config mode arm

A fenti parancs várható kimenete:

        info:   New mode is arm

3. Szükség esetén az **azure group create** parancs futtatásával hozzon létre egy új erőforráscsoportot, a lent látható módon. Figyelje meg a parancs kimenetét. A kimenet után látható lista ismerteti a használt paramétereket. További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../resource-group-overview.md).

        azure group create -n appgatewayRG -l eastus

**-n (vagy --name)**. Az új erőforráscsoport neve. A mi esetünkben *appgatewayRG*.

**-l (vagy --location)**. Az Azure-régió, ahol az új erőforráscsoport létrejön. A mi esetünkben *eastus*.

4. Futtassa az **azure group deployment create** parancsmagot, hogy a fent letöltött és módosított sablonnal és paraméterfájlokkal üzembe helyezhesse az új virtuális hálózatot. A kimenet után látható lista ismerteti a használt paramétereket.

        azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

A fenti parancs várható kimenete:

        azure group deployment create -g appgatewayRG -n TestAppgatewayDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestAppgatewayDeployment"
        + Waiting for deployment to complete
        data:    DeploymentName     : TestAppgatewayDeployment
        data:    ResourceGroupName  : appgatewayRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-09-21T20:50:27.5129912Z
        data:    Mode               : Incremental
        data:    Name               Type    Value
        data:    -----------------  ------  --------------
        data:    location           String  East US
        data:    addressPrefix      String  10.0.0.0/16
        data:    subnetPrefix       String  10.0.0.0/24
        data:    skuName            String  Standard_Small
        data:    capacity           Int     2
        data:    backendIpAddress1  String  10.0.1.10
        data:    backendIpAddress2  String  10.0.1.11
        info:    group deployment create command OK

**-g (vagy --resource-group)**. Az erőforráscsoport neve, amelyben az új virtuális hálózat létrejön.

**-f (vagy --template-file)**. Az Azure Resource Manager-sablonfájl elérési útja.

**-e (vagy --parameters-file)**. Az Azure Resource Manager-paraméterfájl elérési útja.

## Az Azure Resource Manager-sablon üzembe helyezése kattintással végrehajtható üzembe helyezéssel

Az Azure Resource Manager-sablonok használatának másik módja a kattintással végrehajtható üzembe helyezés. Ez egy egyszerű mód a sablonok Azure portállal történő használatára.


### 1. lépés
Lépjen az [Application Gateway létrehozása nyilvános IP-címmel](https://azure.microsoft.com/documentation/templates/101-application-gateway-public-ip/) című szakaszra.


### 2. lépés

Kattintson az **Üzembe helyezés az Azure-ban** lehetőségre.

![Üzembe helyezés az Azure-ban](./media/application-gateway-create-gateway-arm-template/deploytoazure.png)

### 3. lépés

Töltse ki a központi telepítési sablon paramétereit a portálon, majd kattintson az **OK** gombra.

![Paraméterek](./media/application-gateway-create-gateway-arm-template/ibiza1.png)

### 4. lépés

Válassza ki a **Jogi feltételek** elemet, és kattintson a **Vásárlás** lehetőségre.

### 5. lépés

Az Egyéni üzembe helyezés panelen kattintson a **Létrehozás** gombra.



## Következő lépések

Ha SSL-alapú kiszervezést szeretne konfigurálni: [Application Gateway konfigurálása SSL-alapú kiszervezéshez](application-gateway-ssl.md).

Ha konfigurálni szeretne egy ILB-vel használni kívánt Application Gateway-t: [Application Gateway létrehozása belső terheléselosztóval (ILB)](application-gateway-ilb.md).

Ha további általános információra van szüksége a terheléselosztás beállításaival kapcsolatban:

- [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)



<!--HONumber=jun16_HO2-->


