---
title: "Az MSI-hozzáférés hozzárendelése egy Azure-erőforrás, az Azure portál használatával"
description: "Részletes útmutatást ad egy olyan MSI Csomaghoz, egy erőforrás-hozzáférés hozzárendelése egy másik erőforrás, az Azure portál használatával."
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 9120104955aac8ca8a0568e4519c99e1bd786541
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>Egy felügyelt Szolgáltatásidentitás hozzáférés hozzárendelése egy erőforrást az Azure portál használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurálta az Azure-erőforrás a egy felügyelt szolgáltatás Identity (MSI), a MSI hozzáférést biztosíthat más erőforráshoz, csakúgy, mint bármely rendszerbiztonsági tag. Ez a cikk bemutatja, hogyan adhat egy Azure virtuális géphez vagy virtuális gép méretezési készlet MSI access Azure storage-fiók, az Azure portál használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Az MSI-hozzáférés hozzárendelése egy másik erőforrás RBAC használata

Engedélyezését MSI a egy Azure-erőforrás, például egy [Azure virtuális gép](qs-configure-portal-windows-vm.md) vagy [Azure VMSS](qs-configure-portal-windows-vmss.md):

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely alatt a MSI konfigurált Azure-előfizetéshez társítva.

2. Nyissa meg a kívánt erőforrás hozzáférés-vezérlés módosítani kívánja. Ebben a példában azt ad az Azure virtuális géphez, és Azure virtuális gép méretezési hozzáférés tárfiókba, így azt lépjen a tárfiókhoz.

3. Egy Azure virtuális géphez, válassza ki a **hozzáférés-vezérlés (IAM)** az erőforrást, majd válassza a lap **+ Hozzáadás**. Adja meg a **szerepkör**, **hozzáférés hozzárendelése a virtuális gép**, és adja meg a megfelelő **előfizetés** és **erőforráscsoport** ahol az erőforrás található. A keresési feltételek területen láthatja az erőforráshoz. Válassza ki az erőforrás, majd **mentése**. 

   ![Hozzáférés-vezérlési (IAM) képernyőkép](../media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
   Egy Azure virtuálisgép-méretezési csoport, válassza ki a **hozzáférés-vezérlés (IAM)** az erőforrást, majd válassza a lap **+ Hozzáadás**. Adja meg a **szerepkör**, **való hozzáférés hozzárendelése**. A keresési feltételek területen keresése a virtuálisgép-méretezési készlet. Válassza ki az erőforrás, majd **mentése**.
   
   ![Hozzáférés-vezérlési (IAM) képernyőkép](../media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-before.png)  

4. A fő ismét **hozzáférés-vezérlés (IAM)** lap, ahol jelennek meg új bejegyzést az erőforrás MSI.

    Azure virtuális gépen:

   ![Hozzáférés-vezérlési (IAM) képernyőkép](../media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

    Az Azure virtuálisgép-méretezési készlet:

    ![Hozzáférés-vezérlési (IAM) képernyőkép](../media/msi-howto-assign-access-vmss-portal/assign-access-control-vmss-iam-blade-after.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Ha az erőforrás MSI nem jelenik meg a rendelkezésre álló azonosítók listája, győződjön meg arról, hogy az MSI-fájl helytelenül engedélyezett. Ebben az esetben azt is visszatérhet az Azure virtuális géphez, és ellenőrizze a következőket:

- Tekintse meg a **konfigurációs** lapon, és győződjön meg arról, hogy az érték **engedélyezett MSI** van **Igen**.
- Tekintse meg a **bővítmények** lapon, és győződjön meg arról, hogy az MSI-bővítmény sikeresen telepítve (**bővítmények** lap nem érhető el egy Azure virtuálisgép-méretezési csoport).

Vagy nem megfelelő, ha szüksége lehet, hogy telepítse újra az erőforráson MSI újra, vagy a központi telepítési hiba elhárítása.

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](overview.md).
- Azure virtuális géphez engedélyezheti a MSI [konfigurálása az Azure virtuális gép felügyelt szolgáltatás identitásának (MSI) az Azure portál használatával](qs-configure-portal-windows-vm.md).
- Egy Azure virtuálisgép-méretezési csoport MSI engedélyezéséhez tekintse [konfigurálása az Azure virtuális gép méretezési beállítása felügyelt szolgáltatás Identity (MSI) az Azure portál használatával](qs-configure-portal-windows-vmss.md)


