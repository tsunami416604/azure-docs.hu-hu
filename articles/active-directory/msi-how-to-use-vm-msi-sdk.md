---
title: "Egy Azure virtuális gép felügyelt Szolgáltatásidentitás használata Azure SDK-k"
description: "Az Azure SDK-k használata az Azure virtuális gép MSI mintakódok."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 222acd1624ce025985bf1a3eef40eb124ab7678e
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Egy Azure virtuális gép felügyelt szolgáltatás Identity (MSI) használata az Azure SDK-k 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]Ez a cikk a SDK-mintákat, amelyek bemutatják, használja a megfelelő Azure SDK támogatásának MSI listáját tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Minden minta kódot, a parancsfájl a cikkben azt feltételezi, hogy az ügyfél egy MSI-kompatibilis virtuális gépen futó. A virtuális gép "Csatlakozás" szolgáltatást használja az Azure-portálon távolról csatlakozni a virtuális Gépet. A virtuális gép MSI engedélyezésével kapcsolatos részletekért lásd: [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával](msi-qs-configure-portal-windows-vm.md), vagy a variant cikkekben (a PowerShell, CLI, sablon vagy egy Azure SDK használatával). 

## <a name="sdk-code-samples"></a>SDK-kódmintákat

| SDK             | Kódminta |
| --------------- | ----------- |
| .NET            | [Egy Azure Resource Manager sablon felügyelt szolgáltatás identitás használatával Windows virtuális gép virtuális gép üzembe helyezése](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Azure-szolgáltatások hívja Service identitásával felügyelt Linux virtuális gép](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Felügyelt identitás-erőforrások kezelése](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [MSI hitelesítéshez használni kívánt egyszerűen a egy virtuális Gépen belül](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Az MSI-kompatibilis virtuális erőforrások kezelése](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>Kapcsolódó tartalom

- Lásd: [Azure SDK-k](https://azure.microsoft.com/downloads/) Azure SDK-erőforrások teljes listáját, például a szalagtár letöltések, dokumentáció és egyéb.
- Egy Azure virtuális gépen az MSI engedélyezéséről [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával](msi-qs-configure-portal-windows-vm.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.








