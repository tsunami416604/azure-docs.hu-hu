---
title: Egyéni szerepkörök használata az Azure-erőforrásokhoz a PIM |} A Microsoft Docs
description: Ismerje meg, hogyan használható az egyéni szerepkörök az Azure-erőforrásokat az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 2dc3729f533b6f4b70cb3d83cc295ca1afcda5f3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197177"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Egyéni szerepkörök használata az Azure-erőforrásokhoz az PIM-ben

Szüksége lehet a szerepkör egyes tagjai művelet során gondoskodik a nagyobb autonómia mások szigorúakat Privileged Identity Management (PIM) vonatkoznak. Példaként vegyünk egy forgatókönyvet, ahol a szervezet több szerződés hozzárendeli, amelyek segítik az Azure-előfizetésben futó alkalmazás fejlesztésének hires.

Erőforrás-rendszergazdák az alkalmazottakkal való jogosultsághoz a hozzáférés jóváhagyása nélkül kívánja. Azonban minden szerződés hozzárendeli jóvá kell hagyni azokat a szervezet erőforrásaihoz való hozzáférés kérése.

Az Azure-erőforrások szerepköreihez tartozó célzott PIM-beállítások megadása a következő szakaszban leírt lépésekkel.

## <a name="create-the-custom-role"></a>Az egyéni szerepkör létrehozása

Egy egyéni szerepkör létrehozásához kövesse a témakörben ismertetett [egyéni szerepkörök létrehozása az Azure szerepköralapú hozzáférés-vezérlés](../role-based-access-control-custom-roles.md).

Egyéni szerepkör létrehozásakor tartalmaznia kell egy leíró nevet így emlékezzen rá, mely szeretett volna ismétlődő beépített szerepkör.

> [!NOTE]
> Győződjön meg arról, hogy az egyéni szerepkör-e meg az a másolni kívánt szerepkört, és a beépített szerepkör hatókörében megfelel-e.

## <a name="apply-pim-settings"></a>A PIM-beállítások alkalmazása

A saját bérlőjében, az Azure Portalon, a szerepkör létrehozása után nyissa meg a **Privileged Identity Management – Azure-erőforrások** ablaktáblán. Válassza ki az erőforrást, amely a szerepkör vonatkozik.

![A "Privileged Identity Management – Azure-erőforrások" panel](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[A PIM szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md) ezen szerepkör tagjai, amelyek kell alkalmazni.

Végül [szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md) tagokat, amelyeket ezek a beállítások-célozni kívánt külön csoportja számára.

## <a name="next-steps"></a>További lépések

- [A PIM Azure szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)
- [Egyéni szerepkörök az Azure-ban](../../role-based-access-control/custom-roles.md)
