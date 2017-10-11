---
title: "FQDN létrehozni a Linux virtuális gépek az Azure portálon |} Microsoft Docs"
description: "Megtudhatja, hogyan hozzon létre egy teljesen minősített tartománynevét, vagy teljes tartománynév, az erőforrás-kezelő alapú virtuális gépet az Azure portálon."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 49bfec791fcca3feabc4eb280cefd7faada0ea31
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Hozzon létre egy teljesen minősített tartománynevét a Linux virtuális gépek Azure-portálon

Amikor a virtuális gép (VM) hoz létre a [Azure-portálon](https://portal.azure.com), egy nyilvános IP-erőforrás a virtuális gép automatikusan létrejön. Az IP-cím segítségével érheti el távolról a virtuális Gépet. Bár a portál nem hoz létre egy [teljesen minősített tartománynevét](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), vagy teljes Tartománynevét, adhat hozzá egy virtuális gép létrehozása után. Ez a cikk azt mutatja be, a DNS-nevét vagy teljes tartománynév létrehozásához szükséges lépéseket.

## <a name="create-a-fqdn"></a>Hozzon létre egy teljesen minősített Tartományneve
Ez a cikk feltételezi, hogy már létrehozta a virtuális gépek. Ha szükséges, akkor [hozzon létre egy virtuális Gépet a portálon](quick-create-portal.md) vagy [az Azure parancssori felülettel](quick-create-cli.md). Miután a virtuális gép megfelelően működik, és kövesse az alábbi lépéseket:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Most csatlakozhat távolról a virtuális gép használata a DNS-név, többek között a `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Következő lépések
Most, hogy a virtuális gép nyilvános IP-cím és DNS névvel rendelkezik, akkor általános alkalmazás-keretrendszerbeli vagy szolgáltatásokat tudjanak telepíteni nginx, a MongoDB, a Docker, például stb.

Akkor is olvashat további tudnivalókat [erőforrás-kezelő használatával](../../azure-resource-manager/resource-group-overview.md) kapcsolatos tippek felépítése az Azure-környezetekhez.

