---
title: Felügyelt identitások használata Azure-beli virtuális gépeken az Azure SDK-kkal – Azure AD
description: Kódminták at Azure SDK-k egy Azure virtuális gép, amely felügyelt identitások az Azure-erőforrások használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 422b4c63a86904721fc6910193ffa63a8fe46f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184153"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Felügyelt identitások használata Azure-erőforrásokhoz Azure-beli virtuális gépeken az Azure SDK-kkal 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ez a cikk az SDK-minták listáját tartalmazza, amelyek bemutatják a megfelelő Azure SDK-k által az Azure-erőforrások felügyelt identitások használatára vonatkozó használatát.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Ebben a cikkben az összes mintakód/parancsfájl feltételezi, hogy az ügyfél egy virtuális gépen fut, amely en engedélyezve van az Azure-erőforrások felügyelt identitásai. Használja a virtuális gép "Connect" funkciót az Azure Portalon, távolról csatlakozhat a virtuális géphez. Az Azure-erőforrások felügyelt identitásainak virtuális gépeken való engedélyezéséről az [Azure-erőforrások felügyelt identitásainak konfigurálása az Azure Portalon,](qs-configure-portal-windows-vm.md)vagy az egyik változatcikk (PowerShell, CLI, sablon vagy Egy Azure SDK használatával) című témakörben olvashat. 

## <a name="sdk-code-samples"></a>SDK-kódminták

| SDK             | Kódminta |
| --------------- | ----------- |
| .NET            | [Azure Resource Manager-sablon üzembe helyezése Windows virtuális gépről az Azure-erőforrások felügyelt identitásai használatával](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Azure-szolgáltatások hívása Linuxos virtuális gépről felügyelt identitások használatával az Azure-erőforrásokhoz](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Erőforrások kezelése az Azure-erőforrások felügyelt identitásai használatával](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Felügyelt identitások használata az Azure-erőforrásokhoz a virtuális gépen belüli egyszerű hitelesítéshez](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Erőforrások kezelése virtuális gépről felügyelt identitásokkal az Azure-erőforrások engedélyezéséhez](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure SDK-k](https://azure.microsoft.com/downloads/) teljes listáját az Azure SDK-erőforrások, beleértve a könyvtár letöltések, dokumentáció és egyebek.
- Ha engedélyezni szeretné az Azure-erőforrások felügyelt identitások egy Azure virtuális gép, [az Azure-erőforrások felügyelt identitások konfigurálása egy virtuális gép en az Azure Portalon.](qs-configure-portal-windows-vm.md)








