---
title: Teljes tartománycsoport létrehozása virtuális géphez az Azure Portalon
description: Megtudhatja, hogyan hozhat létre egy teljesen minősített tartománynevet vagy teljes tartománynevet egy Erőforrás-kezelő alapú virtuális géphez az Azure Portalon.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f51b9ecf8e300af6b1e3f11d8431de7a282ab342
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759351"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Teljesen minősített tartománynév létrehozása az Azure Portalon Linux os virtuális géphez

Amikor virtuális gépet (VM) hoz létre az [Azure Portalon,](https://portal.azure.com)automatikusan létrejön egy nyilvános IP-erőforrás a virtuális gépszámára. Ezzel az IP-címmel távolról érheti el a virtuális gép. Bár a portál nem hoz létre [teljesen minősített tartománynevet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)vagy teljes tartománynevet, a virtuális gép létrehozása után hozzáadhat egyet. Ez a cikk bemutatja a DNS-név vagy teljes tartománynév létrehozásának lépéseit.

## <a name="create-a-fqdn"></a>Teljes tartománykapcsolati csoport létrehozása
Ez a cikk feltételezi, hogy már létrehozott egy virtuális gép. Szükség esetén [létrehozhat egy virtuális gép a portálon](quick-create-portal.md) vagy az Azure [CLI.](quick-create-cli.md) Kövesse az alábbi lépéseket, ha a virtuális gép működik:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Most már csatlakozhat távolról a virtuális géphez ezzel `ssh azureuser@mydns.westus.cloudapp.azure.com`a DNS-névvel, például a használatával.

## <a name="next-steps"></a>További lépések
Most, hogy a virtuális gép rendelkezik egy nyilvános IP-és DNS-név, üzembe helyezheti a közös alkalmazáskeretrendszerek vagy szolgáltatások, például nginx, MongoDB, Docker, stb.

A Resource Manager [használatával](../../azure-resource-manager/management/overview.md) kapcsolatos további információkért az Azure-telepítések létrehozásához.

