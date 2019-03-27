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
ms.openlocfilehash: 6e85bd6ec51cff27fed6d0b2d9e73f94325e4d4f
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500236"
---
# <a name="create-an-azure-firewall-test-environment"></a>Azure Firewall-tesztkörnyezet létrehozása

Ez a példaszkript létrehoz egy tűzfalat és egy hálózati tesztkörnyezetet. A hálózat egy virtuális hálózattal és három alhálózattal rendelkezik: *AzureFirewallSubnet*, *ServersSubnet*, és *JumpboxSubnet*. A ServersSubnet és a JumpboxSubnet tartalmaz egy-egy 2 magos Windows Servert.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A tűzfal az AzureFirewallSubnet alhálózaton található, és egy egyetlen szabályt tartalmazó alkalmazásszabály-gyűjteménnyel van konfigurálva, amely a www.microsoft.com webhelyhez való hozzáférést engedélyezi.

A felhasználó által létrehozott útvonal a ServersSubnet alhálózatról érkező hálózati forgalmat átirányítja a tűzfalon, amelyen a tűzfalszabályok alkalmazva vannak.

A szkriptet az Azure [Cloud Shellben](https://shell.azure.com/powershell) vagy egy helyi PowerShell-telepítésből futtathatja. 

Ha helyileg futtatja a PowerShell, ehhez a szkripthez az Azure PowerShell. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. 

Ha frissítenie kell, használja a `PowerShellGet` megoldást, amely a Windows 10 és a Windows Server 2016 rendszer részét képezi.

> [!NOTE]
>Más Windows-verziókon telepítenie kell a `PowerShellGet` eszközt, mielőtt használhatná. A `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` parancs futtatásával megtudhatja, hogy telepítve van-e a rendszerén. Ha a kimenet üres, telepítenie kell a [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616) legújabb verzióját.

További információkért lásd: [Azure PowerShell telepítése](/powershell/azure/install-Az-ps)

A webplatformos telepítővel telepített bármely meglévő Azure PowerShell ütközni fog a PowerShellGet-telepítéssel, ezért el kell távolítania.

Ne feledje, ha helyileg futtatja a PowerShellt, akkor a `Connect-AzAccount` parancsot is futtatnia kell az Azure-kapcsolat létrehozásához.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

Az alábbi paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás:

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript az alábbi parancsokkal létrehoz egy erőforráscsoportot, egy virtuális hálózatot és hálózati biztonsági csoportokat. Az alábbi táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik:

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Alhálózati konfigurációs objektumot hoz létre. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehoz egy Azure-beli virtuális hálózatot és előtérbeli alhálózatot. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Hálózati biztonsági csoporthoz rendelendő biztonsági szabályokat hoz létre. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |NSG-szabályokat hoz létre, amelyek engedélyeznek vagy blokkolnak adott alhálózatokra mutató bizonyos portokat. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Az NSG-ket alhálózatokhoz társítja. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Nyilvános IP-címet hoz létre, amely lehetővé teszi a virtuális gép elérését az internetről. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Virtuális hálózati adaptereket hoz létre, és a virtuális hálózat előtérbeli és háttérbeli alhálózataihoz csatolja őket. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Egy virtuálisgép-konfigurációt hoz létre. Ebben a konfigurációban olyan információk szerepelnek, mint a virtuális gép neve, az operációs rendszer és a rendszergazdai hitelesítő adatok. A rendszer a virtuális gépek létrehozása során használja ezt a konfigurációt. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Virtuális gépet hoz létre. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. |
|[New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall)| Új Azure Firewallt hoz létre.|
|[Get-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/get-azfirewall)|Lekér egy Azure Firewall-objektumot.|
|[New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule)|Új Azure Firewall alkalmazásszabályt hoz létre.|
|[Set-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/set-azfirewall)|Változtatásokat hajt végre az Azure Firewall-objektumon.|

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

