---
title: Az Azure Security Center – gyakori kérdések – az engedélyekkel kapcsolatos kérdések
description: Ez a gyakori kérdések választ adnak az Azure Security Center, egy olyan termék engedélyekkel kapcsolatos kérdéseire, amelyek segítenek a fenyegetések megelőzésében, észlelésében és az azokra való válaszadásban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 66a260fbb03f770ee98ec29a5f5e15e3d7dd1310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599391"
---
# <a name="permissions"></a>Engedélyek

## <a name="how-do-permissions-work-in-azure-security-center"></a>Hogyan működnek az engedélyek az Azure Security Centerben?

Az Azure Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) használ, amelynek [beépített szerepköreit](../role-based-access-control/built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti.

A Security Center felméri az erőforrások konfigurációját a biztonsági problémák és biztonsági rések azonosítására. A Biztonsági központban csak akkor jelenik meg egy erőforrással kapcsolatos információ, ha tulajdonos, közreműködő vagy olvasó szerepkört kap ahhoz az előfizetéshez vagy erőforráscsoporthoz, amelyhez egy erőforrás tartozik.

További információ a [szerepkörökről](security-center-permissions.md) és az engedélyezett műveletekről a Security Centerben című témakörben olvashat bővebben.



## <a name="who-can-modify-a-security-policy"></a>Ki módosíthatja a biztonsági házirendet?

A biztonsági házirend módosításához biztonsági rendszergazdának vagy az adott előfizetés tulajdonosának vagy közreműködőjének kell lennie.

A biztonsági házirendek konfigurálásáról a [Biztonsági házirendek beállítása az Azure Security Centerben című témakörben](tutorial-security-policy.md)olvashat.