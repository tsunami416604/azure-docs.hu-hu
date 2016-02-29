<properties 
   pageTitle="在傳統部署模型中透過 PowerShell 控制路由和使用虛擬應用裝置 |Microsoft Azure"
   description="了解如何在傳統部署模型中使用 PowerShell 來控制 VNet 中的路由"
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

#透過 PowerShell 控制路由和使用虛擬應用裝置 (傳統) 

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文章涵蓋傳統部署模型。 您也可以 [動作的 ARM 此處輸入](armToken)。

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

以下的 Azure PowerShell 命令範例假設您已根據上述案例建立簡單的環境。 如果您想要執行命令，因為它們會顯示在這份文件，建立環境中所示 [建立 VNet (傳統) 使用 PowerShell](virtual-networks-create-vnet-classic-ps.md)。

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## 建立前端子網路的 UDR
若要根據上述案例建立前端子網路所需的路由表和路由，請依照下列步驟執行。

3. 執行 **`New-AzureRouteTable`** 指令程式可建立前端子網路的路由表。

        ```powershell
        New-AzureRouteTable -Name UDR-FrontEnd `
            -Location uswest `
            -Label "Route table for front end subnet"
        ```

    Output:

        Name         Location   Label                          
        ----         --------   -----                          
        UDR-FrontEnd West US    Route table for front end subnet

4. 執行 **`Set-AzureRoute`** 指令程式可在路由表中建立路由傳送至後端的子網路 (192.168.2.0/24) 的所有流量上面建立 **FW1** VM (192.168.0.4)。
    
        ```powershell
        Get-AzureRouteTable UDR-FrontEnd `
            |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4
        ```

    Output:

        Name     : UDR-FrontEnd
        Location : West US
        Label    : Route table for frontend subnet
        Routes   : 
                   Name                 Address Prefix    Next hop type        Next hop IP address
                   ----                 --------------    -------------        -------------------
                   RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. 執行 **`Set-AzureSubnetRouteTable`** 指令程式可將路由表關聯上面建立與 **前端** 子網路。

        ```powershell
        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName FrontEnd `
            -RouteTableName UDR-FrontEnd
        ```
 
## 建立後端子網路的 UDR
若要根據上述案例建立後端子網路所需的路由表和路徑，請依照下列步驟執行。

3. 執行 **`New-AzureRouteTable`** 指令程式可建立後端的子網路的路由表。

        ```powershell
        New-AzureRouteTable -Name UDR-BackEnd `
            -Location uswest `
            -Label "Route table for back end subnet"
        ```

4. 執行 **`Set-AzureRoute`** 指令程式可在路由表中建立路由上面建立端子 (192.168.1.0/24) 的所有流量傳送至 **FW1** VM (192.168.0.4)。

        ```powershell
        Get-AzureRouteTable UDR-BackEnd `
            |Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4
        ```

5. 執行 **`Set-AzureSubnetRouteTable`** 指令程式可將路由表關聯上面建立與 **後端** 子網路。

        ```powershell
        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName FrontEnd `
            -RouteTableName UDR-FrontEnd
        ```
## 在 FW1 VM 上啟用 IP 轉送
若要在 FW1 VM 中啟用 IP 轉送，請依照下列步驟執行。

1. 執行 **`Get-AzureIPForwarding`** 指令程式來檢查 IP 轉送的狀態

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Get-AzureIPForwarding

    Output:

        Disabled

2. 執行 **`Set-AzureIPForwarding`** 命令以啟用 IP 轉送的 *FW1* VM。

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Set-AzureIPForwarding -Enable
