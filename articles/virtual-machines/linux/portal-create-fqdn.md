---
title: Teljes tartományonkénti tartomány létrehozása Linuxos virtuális géphez az Azure Portalon
description: Megtudhatja, hogyan hozhat létre egy teljesen minősített tartománynevet vagy teljes tartománynevet egy Erőforrás-kezelő alapú virtuális géphez az Azure Portalon.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: caf7c1c9c0704de1408a322f581019eb74443365
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461497"
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

