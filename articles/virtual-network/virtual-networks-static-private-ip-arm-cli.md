<properties 
   pageTitle="如何在 ARM 模式中使用 CLI 設定靜態私人 IP| Microsoft Azure"
   description="了解靜態 IP (DIP) 以及如何在 ARM 模式下使用 CLI 管理它們"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# 如何在 Azure CLI 中設定靜態私人 IP 位址

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文涵蓋了資源管理員部署模型。 您也可以 [管理傳統部署模型中的靜態私人 IP 位址](virtual-networks-static-private-ip-classic-cli.md)。

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

下列範例 Azure CLI 命令會預期已經建立簡單的環境。 如果您想要執行命令，因為它們會顯示在這份文件，先建立測試環境中所述 [建立 vnet](virtual-networks-create-vnet-arm-cli.md)。

## 建立 VM 時如何指定靜態私人 IP 位址
若要建立名為的 VM *DNS01* 中 *前端* 名為 VNet 的子網路 *TestVNet* 固定的私人 ip *192.168.1.101*, ，依照下列步驟:

1. 如果您從未使用 Azure CLI，請參閱 [安裝和設定 Azure CLI](xplat-cli-install.md) 並遵循指示，選取您的 Azure 帳戶和訂閱為止。

2. 執行 **azure 組態模式** 命令以切換至資源管理員模式，如下所示。

        azure config mode arm

    預期的輸出：

        info:    New mode is arm

3. 執行 **azure 網路的公用 ip 建立** vm 建立公用 IP。 輸出後顯示的清單可說明所使用的參數。

        azure network public-ip create -g TestRG -n TestPIP -l centralus
    
    預期的輸出：

        info:    Executing command network public-ip create
        + Looking up the public ip "TestPIP"
        + Creating public ip address "TestPIP"
        + Looking up the public ip "TestPIP"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
        data:    Name                            : TestPIP
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        info:    network public-ip create command OK

    - **-g (或--資源群組)**。 要在其中建立公用 IP 的資源群組名稱。
    - **-n (或--名稱)**。 公用 IP 的名稱。
    - **-l (或--location)**。 將要建立公用 IP 的 Azure 區域。 我們的案例， *centralus*。

3. 執行 **azure 網路的 nic 建立** 命令來建立靜態私人 ip 位址的 NIC。 輸出後顯示的清單可說明所使用的參數。

        azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd

    預期的輸出：

        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

    - **-a (或-私用 ip 位址)**。 NIC 的靜態私人 IP 位址
    - **-m (或--子網路的 vnet 名稱)**。 將建立 NIC 之 VNet 的名稱。
    - **-k (或-子網路名稱)**。 將建立 NIC 之子網路的名稱。

4. 執行 **azure vm 建立** 命令來建立使用公用 IP 與上面所建立的 NIC 的 VM。 輸出後顯示的清單可說明所使用的參數。

        azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd

    預期的輸出：

        info:    Executing command vm create
        + Looking up the VM "DNS01"
        info:    Using the VM Size "Standard_A1"
        warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account vnetstorage
        + Looking up the NIC "TestNIC"
        info:    Found an existing NIC "TestNIC"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
        info:    Found public ip parameters, trying to setup PublicIP profile
        + Looking up the public ip "TestPIP"
        info:    Found an existing PublicIP "TestPIP"
        info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
        + Updating NIC "TestNIC"
        + Looking up the NIC "TestNIC"
        + Creating VM "DNS01"
        info:    vm create command OK

    - **-y (或-os 類型)**。 類型的作業系統的 VM，或是 *Windows* 或 *Linux*。
    - **-f (或-nic 名稱)**。 VM 將使用的 NIC 名稱。
    - **-i (或--公用 ip 名稱)**。 VM 將使用的公用 IP 名稱。
    - **-F (或-vnet 名稱)**。 將建立 VM 之 VNet 的名稱。
    - **-j (或-vnet 子網路名稱)**。 將建立 VM 之子網路的名稱。

## 如何擷取 VM 的靜態私人 IP 位址資訊

若要檢視使用上述指令碼建立之 VM 的靜態私人 IP 位址資訊，請執行下列 Azure CLI 命令，並觀察值 *私用 IP 配置方法* 和 *私人 IP 位址*:

    azure vm show -g TestRG -n DNS01

預期的輸出：

    info:    Executing command vm show
    + Looking up the VM "DNS01"
    + Looking up the NIC "TestNIC"
    + Looking up the public ip "TestPIP
    data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    ProvisioningState               :Succeeded
    data:    Name                            :DNS01
    data:    Location                        :centralus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:
    data:    Storage Profile:
    data:      Source image:
    data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
    data:
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
    data:
    data:    OS Profile:
    data:      Computer Name                 :DNS01
    data:      User Name                     :adminuser
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-90-1A-A8
    data:          Provisioning State        :Succeeded
    data:          Name                      :TestNIC
    data:          Location                  :centralus
    data:            Private IP alloc-method :Static
    data:            Private IP address      :192.168.1.101
    data:            Public IP address       :40.122.213.159
    info:    vm show command OK

## 如何移除 VM 的靜態私人 IP 位址
您無法從 Azure CLI 的 NIC 移除資源管理員的靜態私人 IP 位址。 您必須建立使用動態 IP 的新 NIC、從 VM 移除先前的 NIC，然後將新的 NIC 加入至 VM。 若要變更上述命令中使用之 VM 的 NIC，請遵循下列步驟。
    
1. 執行 **azure 網路的 nic 建立** 命令來建立新的 NIC 使用動態 IP 配置。 請注意這次如何不需要指定 IP 位址。

        azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd

    預期的輸出：

        info:    Executing command network nic create
        + Looking up the network interface "TestNIC2"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC2"
        + Looking up the network interface "TestNIC2"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
        data:    Name                            : TestNIC2
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : centralus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.6
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

2. 執行 **azure vm 集** 命令來變更 VM 所使用的 NIC。

        azure vm set -g TestRG -n DNS01 -N TestNIC2

    預期的輸出：

        info:    Executing command vm set
        + Looking up the VM "DNS01"
        + Looking up the NIC "TestNIC2"
        + Updating VM "DNS01"
        info:    vm set command OK

3. 如果想要執行 **azure 網路的 nic 刪除** 命令，刪除舊的 nic。

        azure network nic delete -g TestRG -n TestNIC --quiet

    預期的輸出：

        info:    Executing command network nic delete
        + Looking up the network interface "TestNIC"
        + Deleting network interface "TestNIC"
        info:    network nic delete command OK

## 如何將靜態私人 IP 位址新增至現有的 VM
若要將靜態私人 IP 位址新增至使用上述指令碼建立之 VM 所使用的 NIC，請執行下列命令：

    azure netwrok nic set -g TestRG -n TestNIC2 -a 192.168.1.101

預期的輸出：

    info:    Executing command network nic set
    + Looking up the network interface "TestNIC2"
    + Updating network interface "TestNIC2"
    + Looking up the network interface "TestNIC2"
    data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
    data:    Name                            : TestNIC2
    data:    Type                            : Microsoft.Network/networkInterfaces
    data:    Location                        : centralus
    data:    Provisioning state              : Succeeded
    data:    MAC address                     : 00-0D-3A-90-29-25
    data:    Enable IP forwarding            : false
    data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
    data:    IP configurations:
    data:      Name                          : NIC-config
    data:      Provisioning state            : Succeeded
    data:      Private IP address            : 192.168.1.101
    data:      Private IP Allocation Method  : Static
    data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:
    info:    network nic set command OK

## 後續步驟

- 深入了解 [保留公用 IP](../virtual-networks-reserved-public-ip) 位址。
- 深入了解 [執行個體層級公用 IP (ILPIP)](../virtual-networks-instance-level-public-ip) 位址。
- 請參閱 [保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)。

