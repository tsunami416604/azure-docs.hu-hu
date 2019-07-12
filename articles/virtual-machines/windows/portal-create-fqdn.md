---
title: FQDN létrehozása a Windows virtuális gépek az Azure Portalon |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy teljes tartománynév, vagy teljes Tartományneve, a Resource Manager-alapú virtuális gépet az Azure Portalon.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
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
ms.openlocfilehash: 48ad63cb39218f099d9128dae379c7df105764b9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723042"
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

