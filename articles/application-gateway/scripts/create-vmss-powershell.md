---
title: Azure PowerShell-példaszkript – Webes forgalom kezelése | Microsoft Docs
description: Azure PowerShell-példaszkript – Webes forgalom kezelése alkalmazásátjáróval és virtuálisgép-méretezési csoporttal.
services: application-gateway
documentationcenter: networking
author: vhorne
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 89915cdd4ae88f1092c45e1d428679a99f1ac906
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76273402"
---
# <a name="manage-web-traffic-with-azure-powershell"></a>Webes forgalom kezelése az Azure PowerShell használatával

Ez a szkript létrehoz egy alkalmazásátjárót, amely virtuálisgép-méretezési csoportot használ a háttérkiszolgálókhoz. Az alkalmazásátjáró ezután konfigurálható a webes forgalom kezelésére. A szkript futtatása után az alkalmazásátjárót annak nyilvános IP-címével tesztelheti.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss.ps1 "Create application gateway")]

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
| [Új – AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Létrehozza az alhálózatkonfigurációt. |
| [Új – AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Létrehozza a virtuális hálózatot az alhálózatkonfigurációk használatával. |
| [Új – AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Az alkalmazásátjáró nyilvános IP-címét hozza létre. |
| [Új – AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Az alhálózatot az alkalmazásátjáróval társító konfigurációt hozza létre. |
| [Új – AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | A nyilvános IP-címet az alkalmazásátjáróhoz hozzárendelő konfigurációt hozza létre. |
| [Új – AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Hozzárendeli az alkalmazásátjáró elérésére használt portot. |
| [Új – AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Háttérkészletet hoz létre az alkalmazásátjáróhoz. |
| [Új – AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Konfigurálja a háttérkészlet beállításait. |
| [Új – AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Figyelőt hoz létre. |
| [Új – AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Útválasztási szabályt hoz létre. |
| [Új – AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Az alkalmazásátjáró szintjét és kapacitását határozza meg. |
| [Új – AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Egy alkalmazásátjárót hoz létre. |
| [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | Tárolóprofilt hoz létre a méretezési csoporthoz. |
| [Set-AzVmssOsProfile](/powershell/module/az.compute/set-azvmssosprofile) | A méretezési csoport operációs rendszerét adja meg. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | A méretezési csoport hálózati adapterét adja meg. |
| [Új – AzVmss](/powershell/module/az.compute/new-azvm) | Virtuális gép méretezési csoportjának létrehozása. |
| [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) | Lekéri egy alkalmazásátjáró nyilvános IP-címét. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Eltávolít egy erőforráscsoportot és az összes abban található erőforrást. | 

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

Az alkalmazásátjárókkal használható további PowerShell-példaszkripteket [Az Application Gateway dokumentációjában](../powershell-samples.md) találhat.
