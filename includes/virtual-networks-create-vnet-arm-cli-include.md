## 如何透過 Azure CLI 建立 VNet

您可以透過 Azure CLI 管理來自任何 Windows、Linux 或 OSX 電腦的命令提示字元中的 Azure 資源。 若要使用 Azure CLI 建立 VNet，請遵循下列步驟。

1. 如果您從未使用 Azure CLI，請參閱 [安裝和設定 Azure CLI](xplat-cli-install.md) 並遵循指示，選取您的 Azure 帳戶和訂閱為止。
2. 執行 **azure 組態模式** 命令以切換至資源管理員模式，如下所示。

        azure config mode arm

    此為上述命令的預期輸出內容：

        info:    New mode is arm

3. 如果有必要，請執行 **azure 群組建立** 來建立新的資源群組，如下所示。 請查看命令的輸出內容。 輸出後顯示的清單可說明所使用的參數。 如需資源群組的詳細資訊，請瀏覽 [Azure 資源管理員概觀](resource-group-overview.md/#resource-groups)。

        azure group create -n TestRG -l centralus

    此為上述命令的預期輸出內容：

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

    - **-n (或--名稱)**。 新資源群組的名稱。 我們的案例， *TestRG*。
    - **-l (或--location)**。 將會在當中建立新資源群組的 Azure 區域。 我們的案例， *centralus*。

4. 執行 **azure 網路 vnet 建立** 命令來建立 VNet 和子網路，如下所示。 

        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

    此為上述命令的預期輸出內容：

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

    - **-g (或--資源群組)**。 將會在當中建立 VNet 之資源群組的名稱。 我們的案例， *TestRG*。
    - **-n (或--名稱)**。 即將建立的 VNet 名稱。 我們的案例， *TestVNet*
    - **-a (或-位址首碼)**。 用於 VNet 位址空間的 CIDR 區塊清單。 我們的案例， *192.168.0.0/16*
    - **-l (或--location)**。 將要建立 VNet 的 Azure 區域。 我們的案例， *centralus*。

5. 執行 **azure 網路的 vnet 子網路建立** 命令來建立子網路，如下所示。 請查看命令的輸出內容。 輸出後顯示的清單可說明所使用的參數。

        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

    此為上述命令的預期輸出內容：

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

    - **-e (或-vnet 名稱**。 將會建立子網路的 VNet 名稱。 我們的案例， *TestVNet*。
    - **-n (或--名稱)**。 新子網路的名稱。 我們的案例， *前端*。
    - **-a (或-位址首碼)**。 子網路 CIDR 區塊。 四個案例中， *192.168.1.0/24*。

6. 如有必要，請重複上述的步驟 5，以建立其他子網路。 我們的案例中，執行下列命令來建立 *後端* 子網路。

        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. 執行 **azure 網路 vnet 顯示** 命令來檢視新的 vnet 的屬性，如下所示。

        azure network vnet show -g TestRG -n TestVNet

    此為上述命令的預期輸出內容：

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

