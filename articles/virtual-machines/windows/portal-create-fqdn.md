---
title: Teljes tartománycsoport létrehozása Windows virtuális géphez az Azure Portalon
description: Megtudhatja, hogyan hozhat létre egy teljesen minősített tartománynevet vagy teljes tartománynevet egy Erőforrás-kezelő alapú virtuális géphez az Azure Portalon.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa4e4bd7561a7a745e6eb0b70016144b9fb57998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75371275"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Teljesen minősített tartománynév létrehozása az Azure Portalon Windows virtuális géphez

Amikor virtuális gépet (VM) hoz létre az [Azure Portalon,](https://portal.azure.com)automatikusan létrejön egy nyilvános IP-erőforrás a virtuális gépszámára. Ezzel az IP-címmel távolról érheti el a virtuális gép. Bár a portál nem hoz létre [teljesen minősített tartománynevet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)vagy teljes tartománynevet, a virtuális gép létrehozása után létrehozhat egyet. Ez a cikk bemutatja a DNS-név vagy teljes tartománynév létrehozásának lépéseit.

## <a name="create-a-fqdn"></a>Teljes tartománykapcsolati csoport létrehozása
Ez a cikk feltételezi, hogy már létrehozott egy virtuális gép. Szükség esetén [létrehozhat egy virtuális gép a portálon](quick-create-portal.md) vagy az Azure [PowerShell.](quick-create-powershell.md) Kövesse az alábbi lépéseket, ha a virtuális gép működik:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Most már csatlakozhat távolról a virtuális géphez ezzel a DNS-névvel, például a Távoli asztali protokoll (RDP) használatával.

## <a name="next-steps"></a>További lépések
Most, hogy a virtuális gép rendelkezik egy nyilvános IP- és DNS-névvel, üzembe helyezheti a közös alkalmazáskeretrendszereket vagy szolgáltatásokat, például az IIS-t, az SQL-t vagy a SharePointot.

A Resource Manager [használatával](../../azure-resource-manager/management/overview.md) kapcsolatos további információkért az Azure-telepítések létrehozásához.

