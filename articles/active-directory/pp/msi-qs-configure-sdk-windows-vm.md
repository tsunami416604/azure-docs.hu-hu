---
title: "A felhasználó által hozzárendelt MSI konfigurálása az Azure virtuális gép az Azure SDK használatával"
description: "Lépés lépés útmutatást, a felhasználó által hozzárendelt felügyelt szolgáltatás Identity (MSI) konfigurálása az Azure virtuális gép, egy Azure SDK használatával."
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
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 19b6179803b8de4102818c1522b00e6b4881d0f0
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-sdk"></a>A felhasználó által hozzárendelt felügyelt szolgáltatás Identity (MSI) konfigurálja a virtuális gépek, az Azure SDK használatával

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt Szolgáltatásidentitás az Azure Active Directory-identitással felügyelt Azure-szolgáltatásokhoz biztosít. Ezzel az identitással használhatja az Azure AD-alapú hitelesítés, anélkül, hogy hitelesítő adatok a kódban támogató szolgáltatások felé történő hitelesítésre. 

Ebből a cikkből megismerheti, hogyan engedélyezheti, és távolítsa el a felhasználó által hozzárendelt MSI egy Azure virtuális gép, egy Azure SDK használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="azure-sdks-with-msi-support"></a>MSI-támogatással rendelkező Azure SDK-k 

Azure végig több programozási platformot támogat [Azure SDK-k](https://azure.microsoft.com/downloads). Ezek MSI támogatják, és megfelelő minták használati bemutatásához frissítve lett. Ez a lista frissül a további támogatja:

| SDK | Sample |
| --- | ------ | 
| Python | [Hozzon létre egy virtuális gép MSI engedélyezve](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Egy olyan MSI Csomaghoz, az Azure virtuális gép létrehozása](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>További lépések

- Olvassa el a kapcsolódó cikkeket az "Konfigurálása MSI-fájl számára egy Azure virtuális gép", megtudhatja, hogyan konfigurálhat egy felhasználó által hozzárendelt MSI egy Azure virtuális gépen.

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
