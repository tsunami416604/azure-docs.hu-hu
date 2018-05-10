---
title: MSI konfigurálása az Azure virtuális gép az Azure portál használatával
description: Lépés által felügyelt szolgáltatás identitásának (MSI) konfigurálásához egy Azure virtuális gépen, az Azure portál használatával részletes utasításokat.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: daveba
ms.openlocfilehash: 62b8504f5c10f338539d263bb231cf96eb405ba6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ön ebből a cikkből megtudhatja, hogyan engedélyezheti vagy letilthatja a hozzárendelt azonosító egy Azure virtuális gép, az Azure portál használatával a rendszer. Hozzárendelése és eltávolítása a felhasználói identitások Azure virtuális gépek jelenleg nem támogatott az Azure portálon.

> [!NOTE]
> A felhasználói identitás műveletek jelenleg nem támogatottak az Azure portálon. Biztonsági frissítések ellenőrzése. 

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a felügyelt Szolgáltatásidentitás, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.

## <a name="managed-service-identity-during-creation-of-an-azure-vm"></a>Felügyelt Szolgáltatásidentitást az Azure virtuális gép létrehozása

Virtuális gép létrehozása az Azure-portálon jelenleg nem támogatja a felügyelt Szolgáltatásidentitás műveleteket. Ehelyett tekintse meg a következő virtuális gép létrehozása gyors üzembe helyezés cikkekben először létre kell hoznia egy virtuális Gépet:

- [Windows virtuális gép létrehozása az Azure portállal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Linux virtuális gép létrehozása az Azure portállal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)  

Lépjen a következő szakaszban talál részletes információt engedélyezése felügyelt Szolgáltatásidentitás a virtuális Gépen.

## <a name="enable-managed-service-identity-on-an-existing-azure-vm"></a>Szolgáltatásidentitás felügyelt egy meglévő Azure virtuális gépen engedélyezése

A rendszer, amely eredetileg lett kiépítve nélkül identitásának hozzárendelt engedélyezése:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) társított Azure-előfizetést, amely tartalmazza a virtuális Gépet egy olyan fiók használatával. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedéllyel.

2. Keresse meg a használni kívánt virtuális gépet, és válassza a "Konfiguráció" lapot.

3. Engedélyezéséhez a virtuális Géphez hozzárendelt rendszer identitásának jelölje ki "Yes" a "Felügyelt szolgáltatásidentitás", és kattintson a **mentése**. Ez a művelet eltarthat 60 másodperc, vagy a több befejezése:

    > [!NOTE]
    > Egy felhasználó lehet hozzárendelve identitás ad hozzá egy virtuális gép jelenleg nem támogatott az Azure portálon.

   ![Konfiguráció lap képernyőképe](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

## <a name="remove-managed-service-identity-from-an-azure-vm"></a>Távolítsa el az Azure virtuális gép felügyelt identitás

Ha egy virtuális gép, amely már nincs szüksége a hozzárendelt rendszeridentitás:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) társított Azure-előfizetést, amely tartalmazza a virtuális Gépet egy olyan fiók használatával. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedéllyel.

2. Keresse meg a használni kívánt virtuális gépet, és válassza a "Konfiguráció" lapot.

3. Tiltsa le a rendszer a virtuális gép identitásának rendelt "Felügyelt szolgáltatás identitás" a "nem" kiválasztásával, majd kattintson a Mentés gombra. Ez a művelet eltarthat 60 másodperc, vagy a több befejezése:

    > [!NOTE]
    > Egy felhasználó lehet hozzárendelve identitás ad hozzá egy virtuális gép jelenleg nem támogatott az Azure portálon.

   ![Konfiguráció lap képernyőképe](../media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)  

## <a name="related-content"></a>Kapcsolódó tartalom

- Felügyelt Szolgáltatásidentitás áttekintését lásd: [áttekintése](overview.md).

## <a name="next-steps"></a>További lépések

- Az Azure portálon adjon egy Azure virtuális gép MSI [egy másik Azure-erőforrás elérésére](howto-assign-access-portal.md).

