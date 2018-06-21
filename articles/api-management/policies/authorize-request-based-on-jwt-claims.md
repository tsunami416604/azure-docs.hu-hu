---
title: Az Azure API management házirend mintát,-JWT jogcímei alapján hozzáférés engedélyezése |} Microsoft Docs
description: Az Azure API management házirend minta - mutatja be az API-k JWT jogcímei alapján meghatározott HTTP-metódus hozzáférés hitelesítése.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 78ea1b51dea0a65aede0e75e3a8c1d424689854e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285372"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Engedélyezi a hozzáférést a JWT jogcím alapján

Ez a cikk bemutatja az Azure API management házirend minta azt mutatja be, hogyan szeretné engedélyekkel felruházni az API-k JWT jogcímei alapján meghatározott HTTP-metódus elérésére. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

