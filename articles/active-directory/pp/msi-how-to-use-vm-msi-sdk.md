---
title: "Felhasználó által hozzárendelt felügyelt Szolgáltatásidentitás az Azure SDK-k használata a virtuális gép"
description: "Az Azure SDK-k használata a virtuális gép egy felhasználó által hozzárendelt MSI mintakódok."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 808b0357494adac8c8ad7f51e668317e378d290d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="use-azure-sdks-with-a-user-assigned-managed-service-identity-msi"></a>Azure SDK-k használata az egy felhasználó által hozzárendelt felügyelt szolgáltatás identitás (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ez a cikk a SDK-mintákat, amelyek bemutatják, használja a felhasználó által hozzárendelt MSI-fájl a megfelelő Azure SDK támogatása listáját tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

> [!IMPORTANT]
> - Minden minta kódot, a parancsfájl a cikkben azt feltételezi, hogy az ügyfél egy MSI-kompatibilis virtuális gépen futó. A virtuális gép "Csatlakozás" szolgáltatást használja az Azure-portálon távolról csatlakozni a virtuális Gépet. A virtuális gép MSI engedélyezésével kapcsolatos részletekért lásd: [konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure parancssori felület használatával](msi-qs-configure-cli-windows-vm.md), vagy a variant cikkekben (a PowerShell, Azure-portálon, sablon vagy egy Azure SDK használatával). 

## <a name="sdk-code-samples"></a>SDK-kódmintákat

| SDK             | Kódminta |
| --------------- | ----------- |
| Python          | [MSI hitelesítéshez használni kívánt egyszerűen a egy virtuális Gépen belül](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Az MSI-kompatibilis virtuális erőforrások kezelése](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>További lépések

- Lásd: [Azure SDK-k](https://azure.microsoft.com/downloads/) Azure SDK-erőforrások teljes listáját, például a szalagtár letöltések, dokumentáció és egyéb.

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.








