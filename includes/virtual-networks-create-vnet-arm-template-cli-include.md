## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Az ARM-sablon üzembe helyezése az Azure CLI-vel
A letöltött ARM-sablon Azure CLI-vel történő üzembe helyezéséhez kövesse az alábbi lépéseket.

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg [Install and Configure the Azure CLI](../articles/cli-install-nodejs.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Az **`azure config mode`** parancs futtatásával váltson Resource Manager módra a lent látható módon.
   
        azure config mode arm
   
    A fenti parancs várható kimenete:
   
        info:    New mode is arm
3. Szükség esetén az **`azure group create`** futtatásával hozzon létre egy új erőforráscsoportot a lent látható módon. Figyelje meg a parancs kimenetét. A kimenet után látható lista ismerteti a használt paramétereket. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).
   
        azure group create -n TestRG -l centralus
   
    A fenti parancs várható kimenete:
   
        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
   
   * **-n (vagy --name)**. Az új erőforráscsoport neve. A mi esetünkben *TestRG*.
   * **-l (vagy --location)**. Az Azure-régió, ahol az új erőforráscsoport létrejön. A mi esetünkben *centralus*.
4. Futtassa az **`azure group deployment create`** parancsmagot, hogy a fent letöltött és módosított sablonnal és paraméterfájlokkal üzembe helyezhesse az új VNetet. A kimenet után látható lista ismerteti a használt paramétereket.
   
        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
   
    A fenti parancs várható kimenete:
   
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK
   
   * **-g (vagy --resource-group)**. Az erőforráscsoport neve, amelyben az új VNet létrejön.
   * **-f (vagy --template-file)**. Az ARM-sablonfájl elérési útja.
   * **-e (vagy --parameters-file)**. Az ARM-paraméterfájl elérési útja.
5. Futtassa a **`azure network vnet show`** parancsot az új vnet tulajdonságainak megtekintéséhez a lent látható módon.
   
        azure network vnet show -g TestRG -n TestVNet
   
    A fenti parancs várható kimenete:
   
        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK

