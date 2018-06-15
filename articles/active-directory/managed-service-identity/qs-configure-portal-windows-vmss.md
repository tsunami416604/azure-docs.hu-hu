---
title: MSI konfigurálása az Azure virtuálisgép-méretezési beállítása az Azure portál használatával
description: Lépés által felügyelt szolgáltatás identitásának (MSI) lévő Azure VMSS, az Azure portál használatával konfigurálására vonatkozó részletes utasításokat.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: c915c692a12781538e10d367d40e3efe473a6853
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929043"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-the-azure-portal"></a>Konfigurálja a VMSS felügyelt szolgáltatás identitás (MSI) az Azure portál használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ön ebből a cikkből megtudhatja, hogyan engedélyezheti vagy letilthatja a rendszer identitás hozzárendelése egy VMSS, az Azure portál használatával. Hozzárendelése és eltávolítása a felhasználói identitások az Azure VMSS a jelenleg nem támogatott az Azure portálon.

> [!NOTE]
> A felhasználói identitás műveletek jelenleg nem támogatottak az Azure portálon. Biztonsági frissítések ellenőrzése.

## <a name="prerequisites"></a>Előfeltételek


- Ha nem ismeri a felügyelt Szolgáltatásidentitás, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.

## <a name="managed-service-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Felügyelt Szolgáltatásidentitás egy Azure virtuálisgép-méretezési csoport létrehozásakor

Virtuális gép létrehozása az Azure-portálon jelenleg nem támogatja a felügyelt Szolgáltatásidentitás műveleteket. Ehelyett tekintse meg a következő Azure virtuális gép méretezési készlet létrehozása gyors üzembe helyezés cikk először létre kell hoznia egy Azure virtuálisgép-méretezési csoport:

- [Egy virtuálisgép-méretezési készlet létrehozása az Azure portálon](../../virtual-machine-scale-sets/quick-create-portal.md)  

Lépjen a következő szakaszban talál részletes információt MSI engedélyezését a virtuálisgép-méretezési készlet.

## <a name="enable-managed-service-identity-on-an-existing-azure-vmms"></a>Egy meglévő Azure VMMS szolgáltatás identitásának felügyelt engedélyezése

A rendszer, amely eredetileg lett kiépítve nélkül identitásának hozzárendelt engedélyezése:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) társított Azure-előfizetést, amely a virtuálisgép-méretezési csoport tartalmaz egy olyan fiók használatával.

2. Nyissa meg a kívánt virtuálisgép-méretezési készlet.

3. Engedélyezéséhez a virtuális Géphez hozzárendelt rendszer identitásának jelölje ki "Yes" a "Felügyelt szolgáltatásidentitás", és kattintson a **mentése**. Ez a művelet eltarthat 60 másodperc, vagy a több befejezése:

   [![Konfiguráció lap képernyőképe](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png#lightbox)  

## <a name="remove-managed-service-identity-from-an-azure-virtual-machine-scale-set"></a>Egy Azure virtuálisgép-méretezési csoport felügyelt Szolgáltatásidentitás eltávolítása

Ha egy virtuálisgép-méretezési csoport, amely már nem kell egy olyan MSI Csomaghoz:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) társított Azure-előfizetést, amely a virtuálisgép-méretezési csoport tartalmaz egy olyan fiók használatával. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuálisgép-méretezési készlet.

2. Nyissa meg a kívánt virtuálisgép-méretezési készlet.

3. Tiltsa le a rendszer a virtuális gép identitásának rendelt "Felügyelt szolgáltatás identitás" a "nem" kiválasztásával, majd kattintson a Mentés gombra. Ez a művelet eltarthat 60 másodperc, vagy a több befejezése:

   ![Konfiguráció lap képernyőképe](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="related-content"></a>Kapcsolódó tartalom

- Felügyelt Szolgáltatásidentitás áttekintését lásd: [áttekintése](overview.md).

## <a name="next-steps"></a>További lépések

- Az Azure portálon adjon egy Azure virtuális gép méretezési MSI [egy másik Azure-erőforrás elérésére](howto-assign-access-portal.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
