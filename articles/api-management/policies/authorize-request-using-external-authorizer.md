---
title: Az Azure API management policy minta – külső hitelesítő használatával kérelem engedélyezéséhez |} A Microsoft Docs
description: Az Azure API management házirend-minta - bemutatja, hogyan a külső hitelesítő beágyazása egy egyéni vagy az örökölt hitelesítési logikát használó kérelmek engedélyezését végzi.
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
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 70f43a058cfd3818dae1ccfa4b222a7d0d740aee
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979403"
---
# <a name="authorize-requests-using-external-authorizer"></a>Külső hitelesítő-kérések engedélyezése

Ez a cikk bemutatja egy Azure API management házirend minta azt mutatja be, hogyan teheti biztonságossá az API-hozzáférés az egyéni hitelesítés/engedélyezés logikai beágyazása egy külső hitelesítő. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Hozzáférési korlátozások szabályzatok](../api-management-access-restriction-policies.md)
+ [A házirend-minták](../policy-samples.md)
