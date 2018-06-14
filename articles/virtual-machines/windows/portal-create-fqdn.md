---
title: Teljes tartománynév létrehozása a Windows virtuális gépek az Azure portálon |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy teljesen minősített tartománynevét, vagy teljes tartománynév, az erőforrás-kezelő alapú virtuális gépet az Azure portálon.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ebc1ef89b24a9aa21f39e5b05051c16351f08cd
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
ms.locfileid: "26707409"
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

