---
title: FQDN létrehozása a Windows virtuális gépek az Azure Portalon |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy teljes tartománynév, vagy teljes Tartományneve, a Resource Manager-alapú virtuális gépet az Azure Portalon.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 885003863b8d5a5a81adc7f0310bbf2238edc68e
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054694"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Hozzon létre egy teljesen minősített tartománynevét egy Windows virtuális gép az Azure Portalon

Amikor egy virtuális gépet (VM) hoz létre a a [az Azure portal](https://portal.azure.com), automatikusan létrejön a virtuális gép nyilvános IP-erőforrást. Az IP-cím használatával érheti el távolról a virtuális Gépet. Bár a portálon hozzon létre egy [teljesen minősített tartománynevét](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), vagy teljes Tartományneve, a létrehozásához követheti a virtuális gép létrehozása után. Ez a cikk bemutatja a lépéseket a DNS-nevét vagy teljes tartománynév létrehozásához.

## <a name="create-a-fqdn"></a>FQDN létrehozása
Ez a cikk azt feltételezi, hogy már létrehozott egy virtuális Gépet. Ha szükséges, [virtuális gép létrehozása a portálon](quick-create-portal.md) vagy [az Azure PowerShell-lel](quick-create-powershell.md). Miután a virtuális gép működik, és kövesse az alábbi lépéseket:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Ön mostantól távolról csatlakozhat a virtuális Gépet, mint például a távoli asztal protokoll (RDP) a DNS-név használatával.

## <a name="next-steps"></a>További lépések
Most, hogy a virtuális gép rendelkezik egy nyilvános IP-cím és DNS-nevet, telepítheti a közös alkalmazás-keretrendszerek és szolgáltatások, például az IIS, SQL vagy a SharePoint.

Emellett olvashat további [Resource Manager használatával](../../azure-resource-manager/resource-group-overview.md) tippek az Azure-környezetek felépítésével.

