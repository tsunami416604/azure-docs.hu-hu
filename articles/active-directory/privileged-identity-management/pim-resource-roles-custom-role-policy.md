---
title: Egyéni szerepkörök használata az Azure-erőforrásokhoz a Privileged Identity Management-Azure Active Directoryban | Microsoft Docs
description: Ismerje meg, hogyan használhatók az Azure-erőforrások egyéni szerepkörei a Azure AD Privileged Identity Management (PIM) szolgáltatásban.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e940dd0e4c653ed4921a9e8f245aab34107ba95
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895712"
---
# <a name="use-custom-roles-for-azure-resources-in-privileged-identity-management"></a>Egyéni szerepkörök használata az Azure-erőforrásokhoz Privileged Identity Management

Előfordulhat, hogy szigorú Privileged Identity Management (PIM) beállításokat kell alkalmaznia az Azure Active Directory (Azure AD) szervezet egyik kiemelt szerepkörű felhasználójának, ugyanakkor nagyobb önállóságot biztosít mások számára. Vegyünk például egy olyan forgatókönyvet, amelyben a szervezet több szerződést bérel az Azure-előfizetésben futtatott alkalmazások fejlesztésének segítésére.

Erőforrás-rendszergazdaként azt szeretné, hogy az alkalmazottak jóváhagyás nélkül jogosultak legyenek a hozzáférésre. Azonban minden szerződéses társítást jóvá kell hagyni, amikor hozzáférést kérnek a szervezet erőforrásaihoz.

Kövesse a következő szakaszban ismertetett lépéseket az Azure-beli erőforrás-szerepkörökhöz tartozó célként Privileged Identity Management beállítások beállításához.

## <a name="create-the-custom-role"></a>Az egyéni szerepkör létrehozása

Ha egyéni szerepkört szeretne létrehozni egy erőforráshoz, kövesse az [Egyéni szerepkörök létrehozása az Azure szerepköralapú Access Control számára](../role-based-access-control-custom-roles.md)című témakörben ismertetett lépéseket.

Ha egyéni szerepkört hoz létre, akkor adjon meg egy leíró nevet, így könnyen megjegyezhető, hogy melyik beépített szerepkört szeretné duplikálni.

> [!NOTE]
> Győződjön meg arról, hogy az egyéni szerepkör a duplikálni kívánt beépített szerepkör duplikálása, és hogy a hatóköre megfelel a beépített szerepkörnek.

## <a name="apply-pim-settings"></a>PIM-beállítások alkalmazása

Miután létrehozta a szerepkört az Azure AD-szervezetben, lépjen a Azure Portal **Privileged Identity Management-Azure-erőforrások** lapjára. Válassza ki azt az erőforrást, amelyre a szerepkör vonatkozik.

![A "Privileged Identity Management – Azure-erőforrások" panel](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Konfigurálja Privileged Identity Management szerepkör azon beállításait](pim-resource-roles-configure-role-settings.md) , amelyek a szerepkör ezen tagjaira vonatkoznak.

Végezetül [rendeljen hozzá szerepköröket](pim-resource-roles-assign-roles.md) azon tagok különálló csoportjához, amelyeket meg szeretne célozni ezekkel a beállításokkal.

## <a name="next-steps"></a>Következő lépések

- [Az Azure erőforrás-szerepkör beállításainak konfigurálása Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Egyéni szerepkörök az Azure-ban](../../role-based-access-control/custom-roles.md)
