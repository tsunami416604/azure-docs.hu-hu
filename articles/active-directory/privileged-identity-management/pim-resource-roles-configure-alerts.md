---
title: Biztonsági riasztások konfigurálása az Azure-erőforrásszerepkörökhöz a Kiemelt identitáskezelés – Azure Active Directory | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja a biztonsági riasztásokat az Azure-erőforrás-szerepkörök höz az Azure AD emelt szintű identitáskezelés (PIM) szolgáltatásában.
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
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b938dc808d9b02ad4105d85a5b3125135c51d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74023082"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-privileged-identity-management"></a>Biztonsági riasztások konfigurálása az Azure-erőforrásszerepkörökhöz a Kiemelt identitáskezelés ben

A kiemelt identitáskezelés (PIM) riasztásokat hoz létre, ha gyanús vagy nem biztonságos tevékenység van az Azure Active Directory (Azure AD) szervezetben. Amikor egy riasztás aktiválódik, megjelenik a Riasztások oldalon.

![Azure-erőforrások – Riasztások oldallista riasztás, kockázati szint és szám](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Figyelmeztetések áttekintése

Válasszon ki egy riasztást egy olyan jelentés megtekintéséhez, amely felsorolja a riasztást kiváltó felhasználókat vagy szerepköröket, valamint a szervizelési útmutatást.

![Riasztási jelentés az utolsó vizsgálat időpontjáról, leírásáról, kockázatcsökkentési lépéseiről, típusáról, súlyosságáról, biztonsági hatásáról és a következő időpontban történő leseelődésmódjáról](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Riasztások

| Riasztás | Severity | Eseményindító | Ajánlás |
| --- | --- | --- | --- |
| **Túl sok tulajdonos van hozzárendelve egy erőforráshoz** |Közepes |Túl sok felhasználó rendelkezik tulajdonosi szerepkörvel. |Tekintse át a listában szereplő felhasználókat, és rendeljen hozzá néhányat a kevésbé kiemelt szerepkörökhöz. |
| **Túl sok állandó tulajdonos van hozzárendelve egy erőforráshoz** |Közepes |Túl sok felhasználó van állandóan hozzárendelve egy szerepkörhöz. |Tekintse át a listában szereplő felhasználókat, és rendeljen hozzá újra néhányat a szerepkör használathoz szükséges aktiválásához. |
| **Ismétlődő szerepkör létrehozva** |Közepes |Több szerepkör nek ugyanaz a feltétele. |Csak az egyik szerepkört használja. |

### <a name="severity"></a>Severity

- **Magas**: Irányelvsértés miatt azonnali beavatkozást igényel. 
- **Közepes**: Nem igényel azonnali műveletet, de potenciális irányelvsértést jelez.
- **Alacsony**: Nem igényel azonnali beavatkozást, de előnyben részesített házirend-módosítást javasol.

## <a name="configure-security-alert-settings"></a>Biztonsági riasztásbeállításainak megadása

A Riasztások lapon nyissa meg a **Beállítások lehetőséget.**

![Riasztások lap kiemelt Beállítások kal](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

A környezet és a biztonsági célok eléréséhez testreszabhatja a különböző riasztások beállításait.

![A beállítások engedélyezéséhez és konfigurálásához szolgáló riasztás lapjának beállítása](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>További lépések

- [Az Azure-erőforrásszerepkör-beállítások konfigurálása a Kiemelt identitáskezelés ben](pim-resource-roles-configure-role-settings.md)
