---
title: "Az MSI-kompatibilis Azure virtuális gép az Azure SDK használatával konfigurálása"
description: "Lépés lépés útmutatást, konfigurálásához és használatához a kezelt Service identitás (MSI) egy Azure virtuális gépen, az Azure SDK használatával."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: bryanla
ms.openlocfilehash: d5fb9decd1417a51c85f23d6b178c8a86cf292bd
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-vm-managed-service-identity-msi-using-an-azure-sdk"></a>Konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure SDK használatával

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitás automatikusan felügyelt identitással az Azure Active Directory (AD) Azure-szolgáltatásokhoz biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megismerheti, hogyan engedélyezheti és MSI eltávolítása egy Azure virtuális gép, egy Azure SDK használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-msi-support"></a>MSI-támogatással rendelkező Azure SDK-k 

Azure végig több programozási platformot támogat [Azure SDK-k](https://azure.microsoft.com/downloads). Ezek MSI támogatják, és megfelelő minták használati bemutatásához frissítve lett. Ez a lista frissül a további támogatja:

| SDK | Sample |
| --- | ------ | 
| .NET   | [Az MSI-kompatibilis virtuális gép erőforrás kezelése](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Az MSI-kompatibilis virtuális gép a tárterület kezelése](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Hozzon létre egy virtuális gép MSI engedélyezve](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Hozzon létre egy virtuális gép MSI engedélyezve](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Egy olyan MSI Csomaghoz, az Azure virtuális gép létrehozása](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Következő lépések

- Olvassa el a kapcsolódó cikkeket az "Konfigurálása MSI-fájl számára egy Azure virtuális gép", megtudhatja, hogyan használhatja az Azure portálon, a PowerShell, a parancssori felület és az erőforrás-sablonok.

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
