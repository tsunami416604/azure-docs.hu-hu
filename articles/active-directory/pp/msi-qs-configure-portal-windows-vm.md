---
title: "MSI konfigurálása az Azure virtuális gép az Azure portál használatával"
description: "Lépés által felügyelt szolgáltatás identitásának (MSI) konfigurálásához egy Azure virtuális gépen, az Azure portál használatával részletes utasításokat."
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
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 37710015904c8112e5d2de504ed5b42895ffb809
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megtudhatja engedélyezése, és távolítsa el az MSI-fájl egy Azure virtuális gép, az Azure portál használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>MSI engedélyezése az Azure virtuális gép létrehozása

A cikk írásának időpontjában MSI engedélyezése egy virtuális Gépet az Azure portálon létrehozása során nem támogatott. Ehelyett tekintse meg a következő virtuális gép létrehozása gyors üzembe helyezés cikkekben először létre kell hoznia egy virtuális Gépet:

- [Windows virtuális gép létrehozása az Azure portállal](~/articles/virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Linux virtuális gép létrehozása az Azure portállal](~/articles/virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Lépjen a következő szakaszban talál részletes információt a virtuális Gépen az MSI engedélyezése.

## <a name="enable-msi-on-an-existing-azure-vm"></a>Egy meglévő Azure virtuális gépen az MSI engedélyezése

Ha egy virtuális Gépet, amely eredetileg lett kiépítve egy olyan MSI Csomaghoz nélkül:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) társított Azure-előfizetést, amely tartalmazza a virtuális Gépet egy olyan fiók használatával. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedéllyel.

2. Nyissa meg a használni kívánt virtuális gépet.

2. Kattintson a "Konfiguráció" lapot, a virtuális Gépen az MSI engedélyezéséhez jelölje ki a "Yes" a "Felügyelt szolgáltatásidentitás", majd **mentése**. Ez a művelet eltarthat 60 másodperc, vagy a több befejezése:

   ![Konfiguráció lap képernyőképe](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-vm"></a>Az Azure virtuális gép MSI eltávolítása

Ha egy virtuális gép, amely már nem kell egy olyan MSI Csomaghoz:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) társított Azure-előfizetést, amely tartalmazza a virtuális Gépet egy olyan fiók használatával. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedéllyel.

2. Nyissa meg a használni kívánt virtuális gépet.

3. Kattintson a "Konfiguráció" lapot, MSI eltávolítása a virtuális gép "Felügyelt szolgáltatás identitás" a "nem" kiválasztásával, majd **mentése**. Ez a művelet eltarthat 60 másodperc, vagy a több befejezése:

   ![Konfiguráció lap képernyőképe](~/articles/active-directory/media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).

## <a name="next-steps"></a>További lépések

- Az Azure portálon adjon egy Azure virtuális gép MSI [egy másik Azure-erőforrás elérésére](msi-howto-assign-access-portal.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
