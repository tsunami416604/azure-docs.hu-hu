---
title: "Teljes tartománynév létrehozása a Windows virtuális gépek az Azure portálon |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy teljesen minősített tartománynevét, vagy teljes tartománynév, az erőforrás-kezelő alapú virtuális gépet az Azure portálon."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d5a555cd873222efcdb29e8eb3aaf128a24414b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Hozzon létre egy teljesen minősített tartománynevét a Windows virtuális gépek Azure-portálon

Amikor a virtuális gép (VM) hoz létre a [Azure-portálon](https://portal.azure.com), egy nyilvános IP-erőforrás a virtuális gép automatikusan létrejön. Az IP-cím segítségével érheti el távolról a virtuális Gépet. Bár a portál nem hoz létre egy [teljesen minősített tartománynevét](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), vagy teljes Tartománynevét, létrehozhat egy virtuális gép létrehozása után. Ez a cikk azt mutatja be, a DNS-nevét vagy teljes tartománynév létrehozásához szükséges lépéseket.

## <a name="create-a-fqdn"></a>Hozzon létre egy teljesen minősített Tartományneve
Ez a cikk feltételezi, hogy már létrehozta a virtuális gépek. Ha szükséges, akkor [hozzon létre egy virtuális Gépet a portálon](quick-create-portal.md) vagy [az Azure PowerShell](quick-create-powershell.md). Miután a virtuális gép megfelelően működik, és kövesse az alábbi lépéseket:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Most csatlakozhat távolról a virtuális Gépet, a DNS-neve, mint a távoli asztal protokoll (RDP) használatával.

## <a name="next-steps"></a>Következő lépések
Most, hogy a virtuális gép nyilvános IP-cím és DNS névvel rendelkezik, általános alkalmazás-keretrendszerbeli vagy szolgáltatásokat, például az IIS, SQL vagy SharePoint telepítheti.

Akkor is olvashat további tudnivalókat [erőforrás-kezelő használatával](../../azure-resource-manager/resource-group-overview.md) kapcsolatos tippek felépítése az Azure-környezetekhez.

