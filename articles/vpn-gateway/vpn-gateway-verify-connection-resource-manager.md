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
ms.openlocfilehash: b59294d07ef64875cb6fbd3e3a49dec61d8b8135
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659403"
---
# <a name="verify-a-vpn-gateway-connection"></a>VPN Gateway-kapcsolatok ellenőrzése

Ez a cikk bemutatja, hogyan ellenőrizheti a VPN Gateway-kapcsolatokat a klasszikus és a Resource Manager-alapú üzemi modellekhez.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Ha a PowerShell használatával szeretné ellenőrizni a Resource Manager-alapú üzemi modell VPN Gateway-kapcsolatát, telepítse az [Azure Resource Manager PowerShell-parancsmagok](/powershell/azure/)legújabb verzióját.

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI használatával szeretné ellenőrizni a Resource Manager-alapú üzemi modell VPN Gateway-kapcsolatát, telepítse a [CLI-parancsok](/cli/azure/install-azure-cli) legújabb verzióját (2,0 vagy újabb).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="azure-portal-classic"></a>(Klasszikus) Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klasszikus)

A klasszikus üzemi modellhez készült VPN Gateway-kapcsolat ellenőrzéséhez a PowerShell használatával telepítse az Azure PowerShell-parancsmagok legújabb verzióit. Ügyeljen arra, hogy letöltse és telepítse a [Service Management](/powershell/azure/servicemanagement/install-azure-ps?#azure-service-management-cmdlets) modult. Az Add-AzureAccount használatával jelentkezzen be a klasszikus üzembehelyezési modellbe.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Következő lépések

* A virtuális hálózatokhoz hozzáadhat virtuális gépeket. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/windows/quick-create-portal.md).