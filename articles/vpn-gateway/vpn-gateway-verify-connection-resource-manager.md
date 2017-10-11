---
title: "A VPN-átjáró kapcsolat ellenőrzése |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan ellenőrizheti egy virtuális hálózat VPN Gateway-kapcsolatot."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: b2d702ecdd5e1fca342e7c84c6e75339097f0bcd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="verify-a-vpn-gateway-connection"></a>A VPN-átjáró kapcsolat ellenőrzése

Ez a cikk bemutatja, hogyan ellenőrizheti a klasszikus és Resource Manager üzembe helyezési modellel VPN gateway-kapcsolattal.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Ha ellenőrizni szeretné a Resource Manager üzembe helyezési modellel PowerShell segítségével a VPN gateway-kapcsolattal, telepítse a legújabb verzióját a [Azure Resource Manager PowerShell-parancsmagok](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Ellenőrizze a Resource Manager üzembe helyezési modellel, az Azure parancssori felület használatával a VPN gateway-kapcsolattal, telepítse a legújabb verzióját a [parancssori felület parancsait](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0-s vagy újabb).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>(Klasszikus) Azure-portálon

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klasszikus)

Ellenőrizze a klasszikus üzembe helyezési modell PowerShell segítségével a VPN gateway-kapcsolatot, telepítse az Azure PowerShell-parancsmagok legújabb verzióját. Ügyeljen arra, hogy töltse le és telepítse a [szolgáltatásfelügyelet](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0) modul. Jelentkezzen be a klasszikus üzembe helyezési modellel a "Hozzáadás-AzureAccount" segítségével.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Következő lépések

* A virtuális hálózatokhoz hozzáadhat virtuális gépeket. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).