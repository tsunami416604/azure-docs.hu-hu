---
title: 'Azure VPN-átjáró: Átjáró-kapcsolat ellenőrzése'
description: Ez a cikk bemutatja, hogyan ellenőrizheti a virtuális hálózati VPN-átjáró kapcsolatot.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 40417b078577424b1ba13ce60f4f773a1c8fd4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780147"
---
# <a name="verify-a-vpn-gateway-connection"></a>VPN-átjárókapcsolat ellenőrzése

Ez a cikk bemutatja, hogyan ellenőrizheti a VPN-átjáró kapcsolat a klasszikus és az Erőforrás-kezelő telepítési modellek.

## <a name="azure-portal"></a>Azure portál

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Ha a PowerShell használatával szeretné ellenőrizni az Erőforrás-kezelő telepítési modelljéhez használt VPN-átjáró-kapcsolatot, telepítse az [Azure Resource Manager PowerShell-parancsmagok](/powershell/azure/overview)legújabb verzióját.

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Ha az Azure CLI használatával szeretné ellenőrizni az Erőforrás-kezelő telepítési modelljéhez használt VPN-átjáró-kapcsolatot, telepítse a [CLI-parancsok](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0-s vagy újabb) legújabb verzióját.

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>(Klasszikus) Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klasszikus)

A klasszikus üzembe helyezési modell VPN-átjáró-kapcsolatának ellenőrzéséhez telepítse az Azure PowerShell-parancsmagok legújabb verzióit. Mindenképpen töltse le és telepítse a [Szolgáltatáskezelés](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) modult. Használja az "Add-AzureAccount" a klasszikus üzembe helyezési modellbe való bejelentkezéshez.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>További lépések

* A virtuális hálózatokhoz hozzáadhat virtuális gépeket. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
