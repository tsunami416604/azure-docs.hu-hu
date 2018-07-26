---
title: A Felügyeltszolgáltatás-identitás konfigurálása Azure virtuális Gépen az Azure SDK-t engedélyezve
description: Lépés útmutató konfigurálásához és használatához a Felügyeltszolgáltatás-identitás-beli virtuális gépen, az Azure SDK-val.
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
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 2763c78d309f5a90d68429caa46581e50f8b4303
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257660"
---
# <a name="configure-a-vm-managed-service-identity-using-an-azure-sdk"></a>Az Azure SDK-val VM-Managed Felügyeltszolgáltatás-identitás konfigurálása

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directory (AD) automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből megismerheti, hogyan engedélyezheti és távolítsa el a Felügyeltszolgáltatás-identitását egy Azure virtuális gép, egy Azure SDK-val.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-service-identity-support"></a>Felügyeltszolgáltatás-identitás támogatása az Azure SDK-k 

Az Azure támogatja több programozási platformon keresztül sorozatát [Azure SDK-k](https://azure.microsoft.com/downloads). Ezek a Felügyeltszolgáltatás-identitás támogatása, és adja meg a megfelelő mintákat a használatot bemutató frissültek. Ez a lista frissül a kiegészítő támogatás:

| SDK | Minta |
| --- | ------ | 
| .NET   | [Erőforrás kezelése MSI-kompatibilis virtuális gépről](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [MSI-kompatibilis virtuális gépről tárhely kezelése](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Engedélyezett MSI-vel rendelkező virtuális gép létrehozása](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Engedélyezett MSI-vel rendelkező virtuális gép létrehozása](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Az Azure virtuális gép létrehozása egy MSI-vel](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>További lépések

- Tekintse meg a kapcsolódó cikkeket "Konfigurálása felügyelt szolgáltatás identitás számára egy Azure virtuális gép", megtudhatja, hogyan használhatja az Azure portal, PowerShell, CLI és erőforrás-sablonok alapján.

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.
