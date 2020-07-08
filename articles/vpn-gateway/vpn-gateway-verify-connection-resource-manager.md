---
title: 'Azure VPN Gateway: átjáró-kapcsolatok ellenőrzése'
description: Ez a cikk bemutatja, hogyan ellenőrizheti a virtuális hálózati VPN Gateway a kapcsolatokat.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 93a7c0bb15c17b6939d6d53018e4fed84b1ae020
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984566"
---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway-kapcsolatok ellenőrzése

Ez a cikk bemutatja, hogyan ellenőrizheti a VPN Gateway-kapcsolatokat a klasszikus és a Resource Manager-alapú üzemi modellekhez.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Ha a PowerShell használatával szeretné ellenőrizni a Resource Manager-alapú üzemi modell VPN Gateway-kapcsolatát, telepítse az [Azure Resource Manager PowerShell-parancsmagok](/powershell/azure/overview)legújabb verzióját.

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI használatával szeretné ellenőrizni a Resource Manager-alapú üzemi modell VPN Gateway-kapcsolatát, telepítse a [CLI-parancsok](https://docs.microsoft.com/cli/azure/install-azure-cli) legújabb verzióját (2,0 vagy újabb).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>(Klasszikus) Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klasszikus)

A klasszikus üzemi modellhez készült VPN Gateway-kapcsolat ellenőrzéséhez a PowerShell használatával telepítse az Azure PowerShell-parancsmagok legújabb verzióit. Ügyeljen arra, hogy letöltse és telepítse a [Service Management](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) modult. Az Add-AzureAccount használatával jelentkezzen be a klasszikus üzembehelyezési modellbe.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>További lépések

* A virtuális hálózatokhoz hozzáadhat virtuális gépeket. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
