---
title: Egy Azure virtuális gépek Felügyeltszolgáltatás-identitás használata az Azure SDK-k
description: Az Azure SDK-k használatával egy Azure virtuális gép MSI-vel Kódminták.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 1c7a0d10f8ff005d90bb77f33bf40a00f97e078f
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901754"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-with-azure-sdks"></a>Egy Azure virtuális gépek Felügyeltszolgáltatás-identitás (MSI) használata az Azure SDK-k 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ez a cikk az SDK-mintákat, amelyek bemutatják a megfelelő Azure SDK-k támogatása az MSI használata listáját tartalmazza.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Az összes kódot minta parancsfájl ebben a cikkben azt feltételezi, hogy az ügyfél egy MSI-kompatibilis virtuális gépen fut. Az Azure Portalon a virtuális gép "Csatlakozás" funkció használatával távolról csatlakozzon a virtuális Géphez. A virtuális gép MSI engedélyezéséhez a részletekért lásd: [konfigurálja a virtuális gépek Felügyeltszolgáltatás-identitás (MSI) az Azure portal használatával](qs-configure-portal-windows-vm.md), vagy az variant cikkekben (a PowerShell, CLI, egy sablon vagy az Azure SDK használatával). 

## <a name="sdk-code-samples"></a>SDK-Kódminták

| SDK             | Kódminta |
| --------------- | ----------- |
| .NET            | [Felügyeltszolgáltatás-identitás használata Windows rendszerű virtuális gépről egy Azure Resource Manager-sablon üzembe helyezése](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Felügyeltszolgáltatás-identitás használata Linux virtuális gép Azure-szolgáltatások hívása](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Felügyeltszolgáltatás-identitás-erőforrások kezelése](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Történő hitelesítéshez egyszerűen csak egy virtuális gépen az MSI használata](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Erőforrások kezelése az MSI-kompatibilis virtuális gépről](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="related-content"></a>Kapcsolódó tartalom

- Lásd: [Azure SDK-k](https://azure.microsoft.com/downloads/) teljes listája megtalálható az Azure SDK-erőforrásokat, többek között a szalagtár letöltések, dokumentáció és egyéb.
- Egy Azure-beli virtuális gépen az MSI engedélyezéséhez tekintse [konfigurálja a virtuális gépek Felügyeltszolgáltatás-identitás (MSI) az Azure portal használatával](qs-configure-portal-windows-vm.md).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.








