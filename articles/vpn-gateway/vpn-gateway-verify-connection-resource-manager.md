---
title: VPN Gateway-kapcsolat ellenőrzése |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan ellenőrizheti egy virtuális hálózati VPN Gateway-kapcsolat.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 9dfea33b558b4b568da1fd9ed90402e6c5ce54bf
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573195"
---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway-kapcsolat ellenőrzése

Ez a cikk bemutatja, hogyan ellenőrizheti a klasszikus és Resource Manager üzembe helyezési modellel a VPN gateway-kapcsolat.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Ellenőrizze a VPN gateway-kapcsolat a Resource Manager-alapú üzemi modellhez, PowerShell-lel, telepítse a legújabb verzióját a [Azure Resource Manager PowerShell-parancsmagok](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

A Resource Manager üzemi modell Azure CLI-vel VPN gateway-kapcsolat ellenőrzése, telepítse a legújabb verzióját a [CLI-parancsok](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0-s vagy újabb).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Az Azure Portalon (klasszikus)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klasszikus)

A klasszikus üzemi modellben a PowerShell használatával a VPN-átjárókapcsolat ellenőrzése, telepítse az Azure PowerShell-parancsmagjainak legújabb verzióit. Ügyeljen arra, hogy töltse le és telepítse a [Service Management](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) modul. Add-AzureAccount használatával jelentkezzen be a klasszikus üzemi modellben.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>További lépések

* A virtuális hálózatokhoz hozzáadhat virtuális gépeket. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).