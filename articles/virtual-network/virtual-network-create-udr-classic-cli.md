<properties 
   pageTitle="在傳統部署模型中使用 Azure CLI 控制路由和使用虛擬應用裝置 | Microsoft Azure"
   description="了解如何在傳統部署模型中使用 Azure CLI 來控制 VNet 中的路由"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />


# 使用 Azure CLI 控制路由和使用虛擬應用裝置 (傳統)

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文章涵蓋傳統部署模型。 您也可以 [控制路由和使用資源管理員部署模型中的虛擬應用裝置](virtual-network-create-udr-arm-cli.md)。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下的範例 Azure CLI 命令是假設您已根據上述案例建立簡單的環境。 如果您想要執行命令，因為它們會顯示在這份文件，建立環境中所示 [建立 VNet (傳統) 使用 Azure CLI](virtual-networks-create-vnet-classic-cli.md)。

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## 建立前端子網路的 UDR

若要根據上述案例建立前端子網路所需的路由表和路由，請依照下列步驟執行。

1. 執行 * *`azure 組態模式`* * 若要切換到傳統模式。

        azure config mode asm

    Output:

        info:    New mode is asm

3. 執行 * *`azure 網路的路由表建立`* * 命令，以建立路由表的前端子。

        azure network route-table create -n UDR-FrontEnd -l uswest

    Output:

        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK

    參數：
    - **-l (或 --location)**。 將要建立新 NSG 的 Azure 區域。 在本文案例中為 *westus*。
    - **-n (or --name)**。 新 NSG 的名稱。 在本文案例中為 *NSG-FrontEnd*。

4. 執行 * *`azure 網路路由表路由組`* * 命令來建立上述建立要傳送到後端的子網路 (192.168.2.0/24) 的所有流量的路由表中的路由到 **FW1** VM (192.168.0.4)。

        azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

    Output:

        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK

    參數：
    - **-r (或--路由資料表名稱)**。 將會加入路由的路由表的名稱。 我們的案例， *UDR 前端*。
    - **-a (或 --address-prefix)**。 封包所指向位置的子網路的位址首碼。 我們的案例， *192.168.2.0/24*。
    - **-t (或--下一個躍點類型)**。 將傳送流量的目標物件類型。 可能的值為 *VirtualAppliance*, ，*VirtualNetworkGateway*, ，*VNETLocal*, ，*網際網路*, ，或 *無*。
    - **-p (或--下一個躍點 ip-位址**)。 下個躍點的 IP 位址。 在本文案例中為 *192.168.0.4*。

5. 執行 * *`azure 網路的 vnet 子網路路由表新增`* * 命令，以將先前建立的路由表關聯 **前端** 子網路。

        azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

    Output:

        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK 

    參數：
    - **-t (或-vnet 名稱)**。 子網路所在的 VNet 名稱。 在本文案例中為 *TestVNet*。
    - **-n (或---**。 路由表將加入的子網路的名稱。 在本文案例中為 *FrontEnd*。

## 建立後端子網路的 UDR

若要根據上述案例建立後端子網路所需的路由表和路徑，請依照下列步驟執行。

3. 執行 * *`azure 網路的路由表建立`* * 命令，以建立路由表的後端的子網路。

        azure network route-table create -n UDR-BackEnd -l uswest

4. 執行 * *`azure 網路路由表路由組`* * 命令來建立上述傳送所有流量端子 (192.168.1.0/24) 所建立的路由表中的路由到 **FW1** VM (192.168.0.4)。

        azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. 執行 * *`azure 網路的 vnet 子網路路由表新增`* * 命令，以將先前建立的路由表關聯 **後端** 子網路。

        azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd







