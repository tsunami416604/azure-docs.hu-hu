---
title: Egyéni szerepkörök használata az Azure-erőforrásokhoz a PIM-Azure Active Directoryban | Microsoft Docs
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
ms.date: 03/30/2018
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d36514c97cf1f45ee0a435d3b716019d2762e5a
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804189"
---
# <a name="use-custom-roles-for-azure-resources-in-pim"></a>Egyéni szerepkörök használata az Azure-erőforrásokhoz a PIM-ben

Előfordulhat, hogy szigorú Azure Active Directory (Azure AD) Privileged Identity Management (PIM) beállításokat kell alkalmaznia egy szerepkör egyes tagjaira, miközben nagyobb önállóságot biztosít mások számára. Vegyünk például egy olyan forgatókönyvet, amelyben a szervezet több szerződést bérel az Azure-előfizetésben futtatott alkalmazások fejlesztéséhez.

Erőforrás-rendszergazdaként azt szeretné, hogy az alkalmazottak jóváhagyás nélkül jogosultak legyenek a hozzáférésre. Azonban minden szerződéses társítást jóvá kell hagyni, amikor hozzáférést kérnek a szervezet erőforrásaihoz.

Kövesse a következő szakaszban ismertetett lépéseket az Azure-erőforrások szerepköreihez tartozó célként megadott PIM-beállítások beállításához.

## <a name="create-the-custom-role"></a>Az egyéni szerepkör létrehozása

Ha egyéni szerepkört szeretne létrehozni egy erőforráshoz, kövesse az [Egyéni szerepkörök létrehozása az Azure szerepköralapú Access Control számára](../role-based-access-control-custom-roles.md)című témakörben ismertetett lépéseket.

Ha egyéni szerepkört hoz létre, akkor adjon meg egy leíró nevet, így könnyen megjegyezhető, hogy melyik beépített szerepkört szeretné duplikálni.

> [!NOTE]
> Győződjön meg arról, hogy az egyéni szerepkör a duplikálni kívánt beépített szerepkör duplikálása, és hogy a hatóköre megfelel a beépített szerepkörnek.

## <a name="apply-pim-settings"></a>PIM-beállítások alkalmazása

Miután létrehozta a szerepkört a bérlőben, a Azure Portal nyissa meg a **Privileged Identity Management-Azure-erőforrások** ablaktáblát. Válassza ki azt az erőforrást, amelyre a szerepkör vonatkozik.

![A "Privileged Identity Management – Azure-erőforrások" panel](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

[Konfigurálja a PIM szerepkör azon beállításait](pim-resource-roles-configure-role-settings.md) , amelyeket alkalmazni kell a szerepkör ezen tagjaira.

Végezetül [rendeljen hozzá szerepköröket](pim-resource-roles-assign-roles.md) azon tagok különálló csoportjához, amelyeket meg szeretne célozni ezekkel a beállításokkal.

## <a name="next-steps"></a>További lépések

- [Az Azure erőforrás-szerepkör beállításainak konfigurálása a PIM-ben](pim-resource-roles-configure-role-settings.md)
- [Egyéni szerepkörök az Azure-ban](../../role-based-access-control/custom-roles.md)
