---
title: Azure PowerShell-példaszkript – Azure Firewall-tesztkörnyezet létrehozása
description: Azure PowerShell-példaszkript – Azure Firewall-tesztkörnyezet létrehozása.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 8/13/2018
ms.author: victorh
ms.openlocfilehash: 3b55767a4375d41b1dc9c4357ca25e562a3cfabe
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438252"
---
# <a name="create-an-azure-firewall-test-environment"></a>Azure Firewall-tesztkörnyezet létrehozása

Ez a példaszkript létrehoz egy tűzfalat és egy hálózati tesztkörnyezetet. A hálózat egy virtuális hálózattal és három alhálózattal rendelkezik: *AzureFirewallSubnet*, *ServersSubnet*, és *JumpboxSubnet*. A ServersSubnet és a JumpboxSubnet tartalmaz egy-egy 2 magos Windows Servert.

A tűzfal az AzureFirewallSubnet alhálózaton található, és egy egyetlen szabályt tartalmazó alkalmazásszabály-gyűjteménnyel van konfigurálva, amely a www.microsoft.com webhelyhez való hozzáférést engedélyezi.

A felhasználó által létrehozott útvonal a ServersSubnet alhálózatról érkező hálózati forgalmat átirányítja a tűzfalon, amelyen a tűzfalszabályok alkalmazva vannak.

A szkriptet az Azure [Cloud Shellben](https://shell.azure.com/powershell) vagy egy helyi PowerShell-telepítésből futtathatja. 

Ha helyileg futtatja a PowerShellt, ehhez a szkripthez az AzureRM PowerShell-modul legújabb (6.9.0-s vagy újabb) verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. 

Ha frissítenie kell, használja a `PowerShellGet` megoldást, amely a Windows 10 és a Windows Server 2016 rendszer részét képezi.

> [!NOTE]
>Más Windows-verziókon telepítenie kell a `PowerShellGet` eszközt, mielőtt használhatná. A `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` parancs futtatásával megtudhatja, hogy telepítve van-e a rendszerén. Ha a kimenet üres, telepítenie kell a [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616) legújabb verzióját.

További információ: [Az Azure PowerShell telepítése Windows rendszeren a PowerShellGet használatával](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.4.0)

A webplatformos telepítővel telepített bármely meglévő Azure PowerShell ütközni fog a PowerShellGet-telepítéssel, ezért el kell távolítania.

Ne feledje, ha helyileg futtatja a PowerShellt, akkor a `Connect-AzureRmAccount` parancsot is futtatnia kell az Azure-kapcsolat létrehozásához.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```powershell
Remove-AzureRmResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript az alábbi parancsokkal létrehoz egy erőforráscsoportot, egy virtuális hálózatot és hálózati biztonsági csoportokat. Az alábbi táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik:

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Alhálózati konfigurációs objektumot hoz létre. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Létrehoz egy Azure-beli virtuális hálózatot és előtérbeli alhálózatot. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Hálózati biztonsági csoporthoz rendelendő biztonsági szabályokat hoz létre. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |NSG-szabályokat hoz létre, amelyek engedélyeznek vagy blokkolnak adott alhálózatokra mutató bizonyos portokat. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Az NSG-ket alhálózatokhoz társítja. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Nyilvános IP-címet hoz létre, amely lehetővé teszi a virtuális gép elérését az internetről. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Virtuális hálózati adaptereket hoz létre, és a virtuális hálózat előtérbeli és háttérbeli alhálózataihoz csatolja őket. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Virtuális gépet hoz létre. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |
|[New-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermfirewall?view=azurermps-6.9.0)| Új Azure Firewallt hoz létre.|
|[Get-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermfirewall?view=azurermps-6.9.0)|Lekér egy Azure Firewall-objektumot.|
|[New-AzureRmFirewallApplicationRule](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermfirewallapplicationrule?view=azurermps-6.9.0)|Új Azure Firewall alkalmazásszabályt hoz létre.|
|[Set-AzureRmFirewall](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermfirewall?view=azurermps-6.9.0)|Változtatásokat hajt végre az Azure Firewall-objektumon.|


## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

