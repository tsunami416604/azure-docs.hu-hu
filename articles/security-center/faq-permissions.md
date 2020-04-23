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
ms.openlocfilehash: 89b3233966ac53d6c384ca6bd5ac30322fbb0b02
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106593"
---
# <a name="permissions"></a>Engedélyek

## <a name="how-do-permissions-work-in-azure-security-center"></a>Hogyan működnek az engedélyek Azure Security Center?

Az Azure Security Center [szerepköralapú hozzáférés-vezérlést (RBAC)](../role-based-access-control/role-assignments-portal.md) használ, amelynek [beépített szerepköreit](../role-based-access-control/built-in-roles.md) az Azure különböző csoportjaihoz, felhasználóihoz és szolgáltatásaihoz rendelheti.

Security Center megvizsgálja az erőforrások konfigurációját a biztonsági problémák és a biztonsági rések azonosítása érdekében. A Security Centerban csak akkor jelenik meg az erőforrásokhoz kapcsolódó információ, ha a tulajdonos, közreműködő vagy olvasó szerepkörét rendelte hozzá ahhoz az előfizetéshez vagy erőforráscsoporthoz, amelyhez az erőforrás tartozik.

A Security Center szerepkörökkel és engedélyezett műveletekkel kapcsolatos további tudnivalókért tekintse meg [a Azure Security Center engedélyeit](security-center-permissions.md) .



## <a name="who-can-modify-a-security-policy"></a>Kik módosíthatják a biztonsági házirendeket?

A biztonsági szabályzatok módosításához biztonsági rendszergazdának vagy az előfizetés tulajdonosának vagy közreműködőnek kell lennie.

A biztonsági szabályzatok konfigurálásával kapcsolatos további információkért lásd: [biztonsági házirendek beállítása Azure Security Centerban](tutorial-security-policy.md).