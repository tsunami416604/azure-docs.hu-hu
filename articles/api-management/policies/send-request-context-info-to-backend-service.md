---
title: Az Azure API management házirend minta – küldési kérelmek környezeti információkat a háttérszolgáltatáshoz |} A Microsoft Docs
description: Az Azure API management házirend-minta - kérelem környezeti információkat küld a háttérszolgáltatás mutatja be.
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
ms.openlocfilehash: 3369f3b3349e8daf9ff540b824c10bbd618a1147
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873427"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Kérelem környezeti információkat küld a háttérszolgáltatáshoz

Ez a cikk bemutatja egy Azure API management házirend minta azt mutatja be, hogyan kérelem környezeti információkat küldhet a háttérszolgáltatáshoz. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)

