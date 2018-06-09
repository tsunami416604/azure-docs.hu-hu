---
title: Egyéni szerepkörök tárolóbeállítások Privileged Identity Management használata Azure-erőforrások |} Microsoft Docs
description: Egyéni szerepkörök az Azure-erőforrások a PIM használatát ismerteti.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: 03904990d54db0dd39ed7059f57a0a13efe0aaca
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233379"
---
# <a name="use-custom-roles-to-target-privileged-identity-management-settings"></a>A Privileged Identity Management tárolóbeállítások egyéni szerepkörök használatával

Szükség lehet a szerepkör egyes tagjai ugyanakkor biztosítható a nagyobb önállóan mások szigorúakat Privileged Identity Management (PIM) vonatkozik. Fontolja meg egy olyan forgatókönyvet, amelyben a szervezet Azure-előfizetés által futtatott alkalmazás fejlesztésének segít több szerződés társult bízza.

Erőforrás-rendszergazdaként alkalmazottaknak anélkül, hogy a jóváhagyási hozzáférés jogosult kíván. Azonban minden szerződés társult jóvá kell hagyni azokat a szervezet erőforrásaihoz való hozzáférés kérése.

Hajtsa végre a megcélzott PIM beállításait az Azure erőforrás-szerepkörök beállítása a következő szakaszban leírt lépéseket.

## <a name="create-the-custom-role"></a>Az egyéni szerepkör létrehozása

Egy egyéni biztonsági szerepkört egy erőforrás létrehozásához kövesse a témakörben ismertetett [egyedi szerepkörök létrehozását, hozzáférés-vezérlési átruházásához](../role-based-access-control-custom-roles.md).

Létrehozhat egyéni biztonsági szerepkört, adja meg egy leíró nevet, mely beépített szerepkör védelméről kíván-e ismétlődő tud jegyezni.

> [!NOTE]
> Győződjön meg arról, hogy az egyéni biztonsági szerepkört a másolni kívánt beépített szerepkör duplikált, és hogy annak hatókörét megegyezik-e a beépített szerepkör.

## <a name="apply-pim-settings"></a>A PIM beállításainak alkalmazása

Miután a szerepkör létrehozása az Ön bérlőjében, az Azure portálon lépjen a **Privileged Identity Management - Azure-erőforrások** ablaktáblán. Válassza ki az erőforrást, amely a szerepkör vonatkozik.

![A "Privileged Identity Management - Azure-erőforrások" ablak](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

[A PIM szerepkör beállításainak konfigurálása](pim-resource-roles-configure-role-settings.md) , hogy ezek a szerepkör tagjai kell alkalmazni.

Végezetül [szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md) ezekkel a beállításokkal megcélozni kívánt tagjainak a különböző csoporthoz.

## <a name="next-steps"></a>További lépések

[Tekintse át az előfizetés-tulajdonosok és a hozzáférés](pim-resource-roles-perform-access-review.md)
