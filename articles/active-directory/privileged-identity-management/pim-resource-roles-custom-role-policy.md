---
title: Egyéni szerepkörök használata az Azure-erőforrásokhoz a PIM-ben – Azure AD | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az egyéni szerepkörök et az Azure-erőforrások az Azure AD kiemelt identitáskezelés (PIM) használatával.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbe08cff2b57155f8f3315f5d3454abfbdad47a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847067"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Egyéni szerepkörök használata az Azure-erőforrásokhoz a Kiemelt identitáskezelés ben

Előfordulhat, hogy szigorú emelt szintű identitáskezelési (PIM) beállításokat kell alkalmaznia egyes, az Azure Active Directory (Azure AD) szervezetben privilegizált szerepkörben lévő felhasználókra, miközben nagyobb autonómiát biztosít mások számára. Fontolja meg például egy olyan forgatókönyvet, amelyben a szervezet több szerződéses munkatársat vesz fel, hogy segítse egy Azure-előfizetésben futó alkalmazás fejlesztését.

Erőforrás-rendszergazdaként azt szeretné, hogy az alkalmazottak jóváhagyás nélkül is jogosultak legyenek a hozzáférésre. Azonban minden szerződéses partnert jóvá kell hagyni, amikor hozzáférést kér a szervezet erőforrásaihoz.

Kövesse a következő szakaszban ismertetett lépéseket az Azure-erőforrás-szerepkörök célzott kiemelt identitáskezelési beállításainak beállításához.

## <a name="create-the-custom-role"></a>Egyéni szerepkör létrehozása

Egyéni szerepkör létrehozásához egy erőforráshoz kövesse az Egyéni szerepkörök létrehozása az [Azure szerepköralapú hozzáférés-vezérléshez című](../role-based-access-control-custom-roles.md)részben leírt lépéseket.

Amikor egyéni szerepkört hoz létre, adjon meg egy leíró nevet, hogy könnyen megjegyezhesse, melyik beépített szerepkört kívánta duplikálni.

> [!NOTE]
> Győződjön meg arról, hogy az egyéni szerepkör a duplikálni kívánt beépített szerepkör másolata, és hogy hatóköre megegyezik-e a beépített szerepkörrel.

## <a name="apply-pim-settings"></a>PIM-beállítások alkalmazása

Miután a szerepkör t az Azure AD-szervezetben jött létre, nyissa meg a **kiemelt identitáskezelés – Azure-erőforrások** lapot az Azure Portalon. Válassza ki azt az erőforrást, amelyre a szerepkör vonatkozik.

![A "Kiemelt identitáskezelés – Azure-erőforrások" ablaktábla](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

A szerepkör ezen tagjaira vonatkozó [kiemelt identitáskezelési szerepkör-beállítások konfigurálása.](pim-resource-roles-configure-role-settings.md)

Végül [rendeljen szerepköröket](pim-resource-roles-assign-roles.md) a tagok azon különálló csoportjához, amelyet ezekkel a beállításokkal meg szeretne célozni.

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrásszerepkör-beállítások konfigurálása a Kiemelt identitáskezelés ben](pim-resource-roles-configure-role-settings.md)
- [Egyéni szerepkörök az Azure-ban](../../role-based-access-control/custom-roles.md)
