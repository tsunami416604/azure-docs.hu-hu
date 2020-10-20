---
title: 'Azure VPN Gateway: átjáró-kapcsolatok ellenőrzése'
description: Ez a cikk bemutatja, hogyan ellenőrizheti a virtuális hálózati VPN Gateway a kapcsolatokat.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: fef9eb49b10008c86ee044a199ae69a43585f4f3
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217971"
---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway-kapcsolatok ellenőrzése

Ez a cikk bemutatja, hogyan ellenőrizheti a VPN Gateway-kapcsolatokat a klasszikus és a Resource Manager-alapú üzemi modellekhez.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Ha a PowerShell használatával szeretné ellenőrizni a Resource Manager-alapú üzemi modell VPN Gateway-kapcsolatát, telepítse az [Azure Resource Manager PowerShell-parancsmagok](/powershell/azure/)legújabb verzióját.

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI használatával szeretné ellenőrizni a Resource Manager-alapú üzemi modell VPN Gateway-kapcsolatát, telepítse a [CLI-parancsok](https://docs.microsoft.com/cli/azure/install-azure-cli) legújabb verzióját (2,0 vagy újabb).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="azure-portal-classic"></a>(Klasszikus) Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klasszikus)

A klasszikus üzemi modellhez készült VPN Gateway-kapcsolat ellenőrzéséhez a PowerShell használatával telepítse az Azure PowerShell-parancsmagok legújabb verzióit. Ügyeljen arra, hogy letöltse és telepítse a [Service Management](/powershell/azure/servicemanagement/install-azure-ps?#azure-service-management-cmdlets) modult. Az Add-AzureAccount használatával jelentkezzen be a klasszikus üzembehelyezési modellbe.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Következő lépések

* A virtuális hálózatokhoz hozzáadhat virtuális gépeket. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/windows/quick-create-portal.md).
