---
title: MSI-hozzáférés hozzárendelése egy Azure-erőforrás, az Azure portal használatával
description: Részletes útmutató egy olyan MSI Csomaghoz, egy erőforrás-hozzáférés hozzárendelése egy másik erőforráshoz, az Azure portal használatával.
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
ms.openlocfilehash: 2af83762fdcd3302f2af9b08585febab42387d79
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035982"
---
# <a name="assign-a-managed-service-identity-access-to-a-resource-by-using-the-azure-portal"></a>A Felügyeltszolgáltatás-identitás hozzáférés hozzárendelése egy erőforrást az Azure portal használatával

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Miután beállította egy Azure-erőforrás és a Felügyeltszolgáltatás-identitás (MSI), az MSI hozzáférést biztosíthat más erőforráshoz, csakúgy, mint bármely rendszerbiztonsági tag. Ez a cikk bemutatja az Azure virtuális gép MSI hozzáférés biztosítása az Azure storage-fiókba, az Azure portal használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Az RBAC használatával az MSI-hozzáférés hozzárendelése egy másik erőforrás

Miután engedélyezte az MSI-Azure-erőforrás [például egy Azure virtuális gép](msi-qs-configure-portal-windows-vm.md):

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely alatt az MSI konfigurálása Azure-előfizetéshez társított.

2. Keresse meg a kívánt erőforrást, amelyre szeretné módosítani a hozzáférés-vezérlés. Ebben a példában azt egy Azure-beli Virtuálisgép-hozzáférés, hogy küldjön egy storage-fiókot, hogy lépjen a tárfiókhoz.

3. Válassza ki a **hozzáférés-vezérlés (IAM)** az erőforrást, és válassza az oldal **+ Hozzáadás**. Adja meg a **szerepkör**, **hozzáférések hozzárendelése a virtuális gép**, és adja meg a megfelelő **előfizetés** és **erőforráscsoport** ahol az erőforrás található. A keresési feltételek területen látnia kell az erőforrás. Válassza ki az erőforrást, és válassza ki **mentése**. 

   ![Hozzáférés-vezérlés (IAM) képernyőképe](../managed-service-identity/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  

4. A fő a rendszer visszairányítja **hozzáférés-vezérlés (IAM)** lap, Itt láthatja az új bejegyzést az erőforrás MSI számára. Ebben a példában a "SimpleWinVM" bemutató erőforráscsoportból a virtuális gép rendelkezik **közreműködői** a tárfiókhoz való hozzáférést.

   ![Hozzáférés-vezérlés (IAM) képernyőképe](../managed-service-identity/media/msi-howto-assign-access-portal/assign-access-control-iam-blade-after.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Az MSI az erőforrás nem jelenik meg a rendelkezésre álló identitások listájában, győződjön meg arról, hogy az MSI engedélyezve lett-e megfelelően. A mi esetünkben azt lépjen vissza az Azure virtuális géphez, és ellenőrizze az alábbiakat:

- Tekintse meg a **konfigurációs** lapon és ellenőrizze, hogy az érték **engedélyezett MSI-vel** van **Igen**.
- Tekintse meg a **bővítmények** lapon és ellenőrizze, hogy a MSI-bővítmény sikeresen telepítve.

Ha vagy helytelen, szüksége lehet ismételt üzembe helyezése az MSI az erőforráson újra, vagy az üzemelő példány hibájának elhárítása.

## <a name="related-content"></a>Kapcsolódó tartalom

- MSI áttekintését lásd: [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).
- Egy Azure-beli virtuális gépen az MSI engedélyezéséhez tekintse [konfigurálása az Azure virtuális gépek Felügyeltszolgáltatás-identitás (MSI) az Azure portal használatával](msi-qs-configure-portal-windows-vm.md).


