---
title: "MSI konfigurálása az Azure virtuálisgép-méretezési beállítása az Azure portál használatával"
description: "Lépés által felügyelt szolgáltatás identitásának (MSI) lévő Azure VMSS, az Azure portál használatával konfigurálására vonatkozó részletes utasításokat."
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 4d308d9cdc0405ee0041c877f5678647343631ab
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="configure-an-azure-virtual-machine-scale-set-managed-service-identity-msi-using-the-azure-portal"></a>Egy Azure virtuális gép méretezési beállítása felügyelt szolgáltatás identitás (MSI) az Azure portál használatával konfigurálása

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megismerheti, hogyan engedélyezheti és egy Azure virtuálisgép-méretezési csoport, az MSI-fájl eltávolítása az Azure portál használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-azure-virtual-machine-scale-set"></a>MSI engedélyezése az Azure virtuálisgép-méretezési csoport létrehozásakor

A cikk írásának időpontjában MSI engedélyezése az Azure portálon beállítani a virtuálisgép-méretezési létrehozása során nem támogatott. Ehelyett tekintse meg a következő Azure virtuális gép méretezési készlet létrehozása gyors üzembe helyezés cikk először létre kell hoznia egy Azure virtuálisgép-méretezési csoport:

- [Egy virtuálisgép-méretezési készlet létrehozása az Azure portálon](../../virtual-machine-scale-sets/virtual-machine-scale-sets-create-portal.md)  

Lépjen a következő szakaszban talál részletes információt MSI engedélyezését a virtuálisgép-méretezési készlet.

## <a name="enable-msi-on-an-existing-azure-vmms"></a>Az egy meglévő Azure VMMS MSI engedélyezése

Ha egy virtuálisgép-méretezési csoport, amely eredetileg lett kiépítve egy olyan MSI Csomaghoz nélkül:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) társított Azure-előfizetést, amely a virtuálisgép-méretezési csoport tartalmaz egy olyan fiók használatával.

2. Nyissa meg a kívánt virtuálisgép-méretezési készlet.

3. Kattintson a **konfigurációs** lapon, a virtuálisgép-méretezési konfigurálása megadásával MSI engedélyezése **Igen** "Felügyelt szolgáltatás identitás", majd kattintson a **mentése**. Ez a művelet eltarthat 60 másodperc, vagy a több befejezése:

   ![Konfiguráció lap képernyőképe](../media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png)  

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Egy Azure virtuálisgép-méretezési csoport MSI eltávolítása

Ha egy virtuálisgép-méretezési csoport, amely már nem kell egy olyan MSI Csomaghoz:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) társított Azure-előfizetést, amely a virtuálisgép-méretezési csoport tartalmaz egy olyan fiók használatával. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuálisgép-méretezési készlet.

2. Nyissa meg a kívánt virtuálisgép-méretezési készlet.

3. Kattintson a **konfigurációs** lapon, távolítsa el a virtuálisgép-méretezési konfigurálása megadásával MSI **nem** alatt **identitás**, majd kattintson a **mentése** . Ez a művelet eltarthat 60 másodperc, vagy a több befejezése:

   ![Konfiguráció lap képernyőképe](../media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)  

## <a name="next-steps"></a>További lépések

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](overview.md).
- Az Azure portálon adjon egy Azure virtuális gép méretezési MSI [egy másik Azure-erőforrás elérésére](howto-assign-access-portal.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
