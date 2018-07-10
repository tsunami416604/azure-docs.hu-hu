---
title: Az MSI-kompatibilis Azure virtuális Gépekhez az Azure SDK-val konfigurálása
description: Lépés útmutató konfigurálásához és használatához a Felügyeltszolgáltatás-identitás (MSI)-beli virtuális gépen, az Azure SDK-val.
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
ms.openlocfilehash: dee4a3e27623150ce3fa648d73542db0cbb23e93
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901442"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>Konfigurálja a VM-Managed Felügyeltszolgáltatás-identitás (MSI) az Azure SDK-val

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directory (AD) automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből megismerheti, hogyan engedélyezheti és távolítsa el az MSI egy Azure virtuális gép, egy Azure SDK-val.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>MSI-támogatással rendelkező Azure SDK-k 

Az Azure támogatja több programozási platformon keresztül sorozatát [Azure SDK-k](https://azure.microsoft.com/downloads). Ezek a MSI támogatják, és megfelelő mintákat a használatot bemutató frissültek. Ez a lista frissül a kiegészítő támogatás:

| SDK | Sample |
| --- | ------ | 
| .NET   | [Erőforrás kezelése MSI-kompatibilis virtuális gépről](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [MSI-kompatibilis virtuális gépről tárhely kezelése](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Engedélyezett MSI-vel rendelkező virtuális gép létrehozása](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Engedélyezett MSI-vel rendelkező virtuális gép létrehozása](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Az Azure virtuális gép létrehozása egy MSI-vel](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>További lépések

- Tekintse meg a kapcsolódó cikkeket "Konfigurálása MSI számára egy Azure virtuális gép", megtudhatja, hogyan használhatja az Azure portal, PowerShell, CLI és erőforrás-sablonok alapján.

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.
