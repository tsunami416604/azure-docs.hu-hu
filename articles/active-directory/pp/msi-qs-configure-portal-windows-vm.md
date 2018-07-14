---
title: Az MSI konfigurálása az Azure virtuális Gépekhez az Azure portal használatával
description: Lépés útmutató konfigurálásához a Felügyeltszolgáltatás-identitás (MSI)-beli virtuális gépen, az Azure portal használatával.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 54bb2466a99569c124128ad3e08f276a0bc0262d
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035265"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurálja a virtuális gépek Felügyeltszolgáltatás-identitás (MSI) az Azure portal használatával

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebben a cikkben, megtudhatja, hogyan engedélyezheti és távolítsa el az MSI egy Azure virtuális gép, az Azure portal használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Az MSI engedélyezéséhez egy Azure virtuális gép létrehozása során

A cikk írásának időpontjában MSI engedélyezéséhez az Azure Portal virtuális gép létrehozása során nem támogatott. Ehelyett olvassa el a következő virtuális gép létrehozása a rövid útmutató cikkek, először hozzon létre egy virtuális gép közül:

- [Windows virtuális gép létrehozása az Azure portal használatával](~/articles/virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Linux rendszerű virtuális gép létrehozása az Azure portal használatával](~/articles/virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Majd folytassa a következő szakaszban a virtuális gép MSI engedélyezéséhez részleteiért.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Egy meglévő Azure virtuális gépen az MSI engedélyezéséhez

Ha rendelkezik egy virtuális Gépet, amely eredetileg egy olyan MSI Csomaghoz nélkül lett üzembe helyezve:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva. Ügyeljen arra, hogy a fiók tartozik egy szerepkör, amely lehetővé teszi a virtuális gép, például a "Virtuális gép közreműködő" írási engedéllyel.

2. Keresse meg a használni kívánt virtuális gépet.

2. Kattintson a "Beállítások" lap, a virtuális gépen az MSI engedélyezéséhez a "Felügyelt szolgáltatásidentitás" "Igen" gombra kattintva, majd kattintson a **mentése**. Ez a művelet is igénybe vehet, 60 másodperc vagy több végrehajtásához:

   ![Konfiguráció lap képernyőképe](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Egy Azure virtuális gép MSI eltávolítása

Ha egy virtuális gép, amely már nincs szüksége egy MSI-csomag:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely tartalmazza a virtuális gép Azure-előfizetés társítva. Ügyeljen arra, hogy a fiók tartozik egy szerepkör, amely lehetővé teszi a virtuális gép, például a "Virtuális gép közreműködő" írási engedéllyel.

2. Keresse meg a használni kívánt virtuális gépet.

3. A "Beállítások" lapon, a virtuális gép eltávolítása MSI Ha a "Nem", "Managed service identity" alatt, majd kattintson **mentése**. Ez a művelet is igénybe vehet, 60 másodperc vagy több végrehajtásához:

   ![Konfiguráció lap képernyőképe](../managed-service-identity/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).

## <a name="next-steps"></a>További lépések

- Egy Azure virtuális gép MSI használata az Azure Portalon adjon [egy másik Azure-erőforrásokhoz való hozzáférés](msi-howto-assign-access-portal.md).

Használja a következő megjegyzéseket visszajelzést, és segítsen finomíthatja és a tartalom formázása.
