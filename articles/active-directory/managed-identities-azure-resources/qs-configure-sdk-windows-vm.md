---
title: Felügyelt identitások konfigurálása SDK használatával – Azure AD
description: Az Azure-erőforrások felügyelt identitások konfigurálásának és használatának lépéses, Azure-sdk használatával.
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
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d11cd51984f82bc20c02669e796d9ba21b9ed5d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74183475"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Virtuális gép konfigurálása felügyelt identitásokkal az Azure-erőforrásokhoz egy Azure SDK használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban (AD). Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot. 

Ebben a cikkben megtudhatja, hogyan engedélyezheti és távolíthatja el az Azure-erőforrások felügyelt identitások egy Azure-beli virtuális gép, egy Azure SDK használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure SDK-k felügyelt identitások az Azure-erőforrások támogatása 

Az Azure több programozási platformot támogat az [Azure SDK-k sorozatán](https://azure.microsoft.com/downloads)keresztül. Ezek közül több is frissítve lett, hogy támogassa az Azure-erőforrások felügyelt identitások, és megfelelő mintákat a használat bemutatásához. Ez a lista a további támogatás sal bővült:

| SDK | Sample |
| --- | ------ | 
| .NET   | [Erőforrás kezelése olyan virtuális gépről, amely engedélyezve van felügyelt identitásokkal az Azure-erőforrások számára engedélyezve](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Az Azure-erőforrások felügyelt identitásaival engedélyezett virtuális gépről történő tárolás kezelése](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Virtuális gép létrehozása a rendszer által kijelölt felügyelt identitás engedélyezésével](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Virtuális gép létrehozása a rendszer által kijelölt felügyelt identitás engedélyezésével](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Azure virtuális gép létrehozása rendszeráltal kijelölt identitással engedélyezve](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>További lépések

- Tekintse meg a kapcsolódó cikkek az **Azure-beli virtuális gép identitásának konfigurálása**területen, megtudhatja, hogyan használhatja az Azure Portal, a PowerShell, a CLI és az erőforrássablonokat.
