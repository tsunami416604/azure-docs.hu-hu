---
title: Azure PowerShell-példaszkript – Webes forgalom korlátozása | Microsoft Docs
description: Azure PowerShell-példaszkript – Alkalmazásátjáró létrehozása webalkalmazási tűzfallal és a forgalom korlátozásához OWASP-szabályokat használó virtuálisgép-méretezési csoporttal.
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: de4a0ecb335e9c44cbb096bb8d17f91963a91378
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57310131"
---
# <a name="restrict-web-traffic-using-azure-powershell"></a>Webes forgalom korlátozása az Azure PowerShell használatával

Ez a szkript létrehoz egy webalkalmazási tűzfallal rendelkező alkalmazásátjárót, amely virtuálisgép-méretezési csoportot használ háttérkiszolgálókként. A webalkalmazási tűzfal OWASP-szabályok alapján korlátozza a webes forgalmat. A szkript futtatása után az alkalmazásátjárót annak nyilvános IP-címével tesztelheti.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss-waf.ps1 "Create application gateway with WAF")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A következő paranccsal eltávolítható az erőforráscsoport, az alkalmazásátjáró és az összes kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja az üzemelő példány létrehozásához. A táblázatban lévő összes elem a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Létrehozza az alhálózatkonfigurációt. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehozza a virtuális hálózatot az alhálózatkonfigurációk használatával. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Az alkalmazásátjáró nyilvános IP-címét hozza létre. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Az alhálózatot az alkalmazásátjáróval társító konfigurációt hozza létre. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | A nyilvános IP-címet az alkalmazásátjáróhoz hozzárendelő konfigurációt hozza létre. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Hozzárendeli az alkalmazásátjáró elérésére használt portot. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Háttérkészletet hoz létre az alkalmazásátjáróhoz. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsettings) | Konfigurálja a háttérkészlet beállításait. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Figyelőt hoz létre. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Útválasztási szabályt hoz létre. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Az alkalmazásátjáró szintjét és kapacitását határozza meg. |
| [New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/new-azapplicationgatewaywebapplicationfirewallconfiguration) | Létrehozza a webalkalmazási tűzfal konfigurációját. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Egy alkalmazásátjárót hoz létre. |
| [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | Tárolóprofilt hoz létre a méretezési csoporthoz. |
| [Set-AzVmssOsProfile](/powershell/module/az.compute/set-azvmssosprofile) | A méretezési csoport operációs rendszerét adja meg. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | A méretezési csoport hálózati adapterét adja meg. |
| [New-AzVmss](/powershell/module/az.compute/new-azvm) | Virtuálisgép-méretezési csoportot hoz létre. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Létrehoz egy tárfiókot. |
| [Set-AzDiagnosticSetting](/powershell/module/az.insights/set-azdiagnosticsetting) | Konfigurálja a diagnosztikát az adatok rögzítéséhez. |
| [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) | Lekéri egy alkalmazásátjáró nyilvános IP-címét. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. | 
## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

Az alkalmazásátjárókkal használható további PowerShell-példaszkripteket [Az Application Gateway dokumentációjában](../powershell-samples.md) találhat.
