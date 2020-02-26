---
title: Azure Security Center GYIK – az engedélyekkel kapcsolatos kérdések
description: Ez a gyakori kérdések a Azure Security Centerban található engedélyekkel kapcsolatos kérdésekre adnak választ, amely segít a fenyegetések megelőzésében, észlelésében és megválaszolásában.
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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599391"
---
# <a name="permissions"></a>Engedélyek

## <a name="how-do-permissions-work-in-azure-security-center"></a>Hogyan működnek az engedélyek Azure Security Center?

Az Azure Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) használ, amelynek [beépített szerepköreit](../role-based-access-control/built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti.

A Security Center a biztonsági problémák és biztonsági rések azonosításához erőforrásán konfiguráció értékeli. A Security Centerben csak láthatja az előfizetés vagy az erőforráscsoport, amely egy erőforrás tartozik, a tulajdonos, közreműködő vagy olvasó a szerepkör hozzárendelése esetén egy erőforráshoz kapcsolódó információkat.

A Security Center szerepkörökkel és engedélyezett műveletekkel kapcsolatos további tudnivalókért tekintse meg [a Azure Security Center engedélyeit](security-center-permissions.md) .



## <a name="who-can-modify-a-security-policy"></a>Ki módosíthatja a biztonsági szabályzatot?

Biztonsági szabályzat módosításához a rendszergazda vagy a tulajdonosának vagy Közreműködőjének előfizetésben kell lennie.

A biztonsági szabályzatok konfigurálásával kapcsolatos további információkért lásd: [biztonsági házirendek beállítása Azure Security Centerban](tutorial-security-policy.md).