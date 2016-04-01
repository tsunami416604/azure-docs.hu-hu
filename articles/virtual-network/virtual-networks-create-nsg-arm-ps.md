<properties 
   pageTitle="如何在 ARM 模式使用 PowerShell 建立 NSG | Microsoft Azure"
   description="了解如何在 ARM 中使用 PowerShell 建立和部署 NSG"
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
   ms.date="11/20/2015"
   ms.author="telmos" />

# 如何在 PowerShell 中建立 NSG

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文涵蓋了資源管理員部署模型。 您也可以 [在傳統部署模型中建立 Nsg](virtual-networks-create-nsg-classic-ps.md)。

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

以下的範例 PowerShell 命令會預期已根據上述案例建立簡單的環境。 如果您想要執行命令，因為它們會顯示在這份文件，先建立測試環境部署 [此範本](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), ，按一下 [ **部署至 Azure**, 、 取代預設參數值，如有必要，並依照入口網站中的指示。

## 如何建立前端子網路的 NSG
若要建立名為 NSG 名為 *NSG 前端* 根據上述情況，依照下列步驟。

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. 如果您從未使用 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md) 並遵循指示以登入 Azure，並選取您的訂閱結束。

3. 建立允許從網際網路存取連接埠 3389 的安全性規則。

        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" `
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
            -SourceAddressPrefix Internet -SourcePortRange * `
            -DestinationAddressPrefix * -DestinationPortRange 3389

4. 建立允許從網際網路存取連接埠 80 的安全性規則。 

        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" `
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
            -SourceAddressPrefix Internet -SourcePortRange * `
            -DestinationAddressPrefix * -DestinationPortRange 80

5. 加入新的 nsg 名為上面建立的規則 **NSG 前端**。

        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-FrontEnd" `
            -SecurityRules $rule1,$rule2

6. 請檢查在 NSG 中建立的規則。

        $nsg

    輸出僅顯示安全性規則：

        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
                                   "Description": "Allow RDP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "3389",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 100,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "web-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
                                   "Description": "Allow HTTP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "80",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 101,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

6. 將 nsg 關聯至上面建立 *前端* 子網路。

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

    僅顯示輸出 *前端* 子網路設定，請注意值 **NetworkSecurityGroup** 屬性 ︰

        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                  },
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                  }
                                ],
                                "NetworkSecurityGroup": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                },
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }

>[AZURE.WARNING] 上述命令的輸出會顯示虛擬網路組態物件，只存在於執行 PowerShell 的電腦的內容。 您需要執行 **組 AzureRmVirtualNetwork** 指令程式可將這些設定儲存至 Azure。

7. 將新的 VNet 設定儲存至 Azure。

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    輸出僅顯示 NSG 部分：

        "NetworkSecurityGroup": {
          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
        }

## 如何建立後端子網路的 NSG
若要建立名為 NSG 名為 *NSG 後端* 根據上述情況，依照下列步驟。

1. 建立允許從前端子網路存取連接埠 1433 (SQL Server 所使用的預設連接埠) 的安全性規則。

        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet" `
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
            -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange * `
            -DestinationAddressPrefix * -DestinationPortRange 1433

4. 建立封鎖存取網際網路的安全性規則。 

        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet" `
            -Access Deny -Protocol * -Direction Outbound -Priority 200 `
            -SourceAddressPrefix * -SourcePortRange * `
            -DestinationAddressPrefix Internet -DestinationPortRange *

5. 加入新的 nsg 名為上面建立的規則 **NSG 後端**。

        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `-Name "NSG-BackEnd" `
            -SecurityRules $rule1,$rule2

6. 將 nsg 關聯至上面建立 *後端* 子網路。

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
            -AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

    僅顯示輸出 *後端* 子網路設定，請注意值 **NetworkSecurityGroup** 屬性 ︰

        Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [...],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

7. 將新的 VNet 設定儲存至 Azure。

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

