---
title: Felügyeltszolgáltatás-identitás konfigurálása egy Azure-beli virtuálisgép-méretezési csoportot, az Azure portal használatával
description: Útmutató az Azure VMSS, az Azure portal használatával konfigurálja a Felügyeltszolgáltatás-identitás lépés.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 93c532cf2864db28b580303ecefec8b6dbed65f6
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257759"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-using-the-azure-portal"></a>A virtuális gép konfigurálása méretezési Felügyeltszolgáltatás-identitás az Azure portal használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebben a cikkben, megtudhatja, hogyan engedélyezheti és tilthatja le a hozzárendelt identitás egy virtuálisgép-méretezési csoportot, a rendszer az Azure portal használatával. Hozzárendelése, illetve a felhasználó által hozzárendelt identitások egy Azure-beli virtuálisgép-méretezési készlet eltávolítása jelenleg nem támogatott az Azure Portalon keresztül.

> [!NOTE]
> A felhasználóhoz hozzárendelt identitás műveletek jelenleg nem támogatottak az Azure Portalon keresztül. Térjen vissza frissítésekért.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ebben a cikkben a kezelési műveletek végrehajtásához a fióknak rendelkeznie kell a következő szerepkör-hozzárendelés:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) engedélyezéséhez, és távolítsa el a rendszer felügyelt identitás hozzárendelt virtuálisgép-méretezési csoportot.

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Felügyeltszolgáltatás-identitás létrehozása az Azure-beli virtuálisgép-méretezési csoportot

A virtuális gép létrehozása az Azure Portalon keresztül jelenleg nem támogatja a Felügyeltszolgáltatás-identitás műveletek. Ehelyett olvassa el az alábbi Azure virtuális gép méretezési készlet létrehozása a rövid útmutató cikkben először hozzon létre egy Azure-beli virtuálisgép-méretezési csoportot:

- [Hozzon létre egy virtuálisgép-méretezési csoportban az Azure Portalon](../../virtual-machine-scale-sets/quick-create-portal.md)  

Majd folytassa a következő szakaszban Felügyeltszolgáltatás-identitás engedélyezése a virtuális gép méretezési részleteiért.

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>Felügyeltszolgáltatás-identitását egy meglévő Azure VMMS a engedélyezése

Ahhoz, hogy a rendszer hozzárendelt identitás eredetileg anélkül, hogy üzembe helyezett virtuális gépen:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoporthoz tartozó fiókkal.

2. Keresse meg a kívánt virtuálisgép-méretezési csoportot.

3. A virtuális gépen a rendszer által hozzárendelt identitással engedélyezése "Igen" kiválasztásával "Managed service identity" alatt, és kattintson a **mentése**. Ez a művelet is igénybe vehet, 60 másodperc vagy több végrehajtásához:

   [![Konfiguráció lap képernyőképe](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Felügyeltszolgáltatás-identitás eltávolítása egy Azure-beli virtuálisgép-méretezési csoportot

Ha egy virtuális gép méretezési csoportot, amely már nincs szüksége a Felügyeltszolgáltatás-identitás van:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoporthoz tartozó fiókkal. Ügyeljen arra, hogy a fiók tartozik egy szerepkör, amely lehetővé teszi a virtuálisgép-méretezési csoportot írási engedéllyel.

2. Keresse meg a kívánt virtuálisgép-méretezési csoportot.

3. Tiltsa le a rendszer, ha a "Nem", "Managed service identity" alatt a virtuális gép identitásának hozzárendelte, majd kattintson a Mentés gombra. Ez a művelet is igénybe vehet, 60 másodperc vagy több végrehajtásához:

   ![Konfiguráció lap képernyőképe](../managed-service-identity/media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>Kapcsolódó tartalom

- Felügyeltszolgáltatás-identitás áttekintését lásd: [áttekintése](overview.md).

## <a name="next-steps"></a>További lépések

- Az Azure Portallal, adja meg az Azure virtuálisgép-méretezési csoport beállítása Felügyeltszolgáltatás-identitás [egy másik Azure-erőforrásokhoz való hozzáférés](howto-assign-access-portal.md).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.
