## VNet létrehozása az Azure CLI használatával

Az Azure CLI segítségével a parancssorból felügyelheti az erőforrásokat bármilyen Windows, Linux vagy OSX rendszert futtató számítógépről. Az alábbi lépésekkel hozhat létre egy VNetet az Azure CLI segítségével.

1. Ha még sosem használta az Azure CLI-t, akkor tekintse meg az [Install and Configure the Azure CLI](../articles/xplat-cli-install.md) (Az Azure CLI telepítése és konfigurálása) részt, és kövesse az utasításokat addig a pontig, ahol ki kell választania az Azure-fiókot és -előfizetést.
2. Az **azure config mode** parancs futtatásával váltson az Erőforrás-kezelő módra, a lent látható módon.

        azure config mode arm

    A fenti parancs várható kimenete:

        info:    New mode is arm

3. Szükség esetén az **azure group create** futtatásával hozzon létre egy új erőforráscsoportot, a lent látható módon. Figyelje meg a parancs kimenetét. A kimenet után látható lista ismerteti a használt paramétereket. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/virtual-network/resource-group-overview.md/#resource-groups) (Az Azure Resource Manager áttekintése).

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

    - **-n (vagy --name)**. Az új erőforráscsoport neve. A mi esetünkben *TestRG*.
    - **-l (vagy --location)**. Az Azure-régió, ahol az új erőforráscsoport létrejön. A mi esetünkben *centralus*.

4. Futtassa az **azure network vnet create** parancsot egy VNet és egy alhálózat létrehozásához az alábbiak szerint. 

        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

    A fenti parancs várható kimenete:

        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK

    - **-g (vagy --resource-group)**. Az erőforráscsoport neve, ahol a VNet létrejön. A mi esetünkben *TestRG*.
    - **-n (vagy --name)**. A létrehozni kívánt VNet neve. A mi esetünkben *TestVNet*
    - **-a (vagy --address-prefixes)**. A VNet címteréhez használt CIDR-blokkok listája. A mi esetünkben *192.168.0.0/16*
    - **-l (vagy --location)**. Az Azure-régió, ahol a VNet létrejön. A mi esetünkben *centralus*.

5. Futtassa az **azure network vnet subnet create** parancsot egy alhálózat létrehozásához az alábbiak szerint. Figyelje meg a parancs kimenetét. A kimenet után látható lista ismerteti a használt paramétereket.

        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

    A fenti parancs várható kimenete:

        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK

    - **-e (vagy --vnet-name**. A VNet neve, ahol létrejön az alhálózat. A mi esetünkben *TestVNet*.
    - **-n (vagy --name)**. Az új alhálózat neve. A mi esetünkben *FrontEnd*.
    - **-a (vagy --address-prefix)**. Az alhálózat CIDR-blokkja. A mi esetünkben *192.168.1.0/24*.

6. Szükség esetén az 5. lépést megismételve hozhat létre egyéb alhálózatokat. A mi esetünkben az alábbi parancsot futtatva hozza létre a *BackEnd* alhálózatot.

        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Futtassa az **azure network vnet show** parancsot az új vnet tulajdonságainak megtekintéséhez a lent látható módon.

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



<!--HONumber=Jun16_HO2-->


