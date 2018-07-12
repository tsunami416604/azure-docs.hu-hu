---
title: FQDN létrehozása a Linux rendszerű virtuális gép az Azure Portalon |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy teljes tartománynév, vagy teljes Tartományneve, a Resource Manager-alapú virtuális gépet az Azure Portalon.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ada68fcb1c480be6d70a925895e968fe714639ac
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232493"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Linux rendszerű virtuális gép létrehozása egy teljesen minősített tartománynevét az Azure Portalon

Amikor egy virtuális gépet (VM) hoz létre a a [az Azure portal](https://portal.azure.com), automatikusan létrejön a virtuális gép nyilvános IP-erőforrást. Az IP-cím használatával érheti el távolról a virtuális Gépet. Bár a portálon hozzon létre egy [teljesen minősített tartománynevét](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), vagy teljes tartománynév, felvehet egy a virtuális gép létrehozása után. Ez a cikk bemutatja a lépéseket a DNS-nevét vagy teljes tartománynév létrehozásához.

## <a name="create-a-fqdn"></a>FQDN létrehozása
Ez a cikk azt feltételezi, hogy már létrehozott egy virtuális Gépet. Ha szükséges, [virtuális gép létrehozása a portálon](quick-create-portal.md) vagy [az Azure CLI-vel](quick-create-cli.md). Miután a virtuális gép működik, és kövesse az alábbi lépéseket:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Ön mostantól távolról csatlakozhat a virtuális gép használata a DNS-névhez, mint például a `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>További lépések
Most, hogy a virtuális gép rendelkezik egy nyilvános IP-cím és DNS-nevet, akkor telepítheti a közös alkalmazás-keretrendszerek vagy szolgáltatások, mint az nginx, a MongoDB, a Docker, stb.

Emellett olvashat további [Resource Manager használatával](../../azure-resource-manager/resource-group-overview.md) tippek az Azure-környezetek felépítésével.

