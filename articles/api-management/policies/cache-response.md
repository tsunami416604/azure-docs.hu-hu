---
title: Az Azure API management policy – képességek háttérszolgáltatás hozzáadása |} A Microsoft Docs
description: Az Azure API management házirend-minta - képességeket adhat a háttérszolgáltatás mutatja be. Lehetővé teheti például, hogy egy időjárás-előrejelző API-ban a szélesség és hosszúság helyett elég legyen egy hely nevét megadni.
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
ms.openlocfilehash: 7c9edbf4b2d231453cd336521a04ba6b7714b696
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873826"
---
# <a name="add-capabilities-to-a-backend-service"></a>Képességek hozzáadása a háttérszolgáltatáshoz

Ez a cikk bemutatja egy Azure API management házirend minta, amely bemutatja, hogyan adhat hozzá funkciókat háttérszolgáltatás. Lehetővé teheti például, hogy egy időjárás-előrejelző API-ban a szélesség és hosszúság helyett elég legyen egy hely nevét megadni. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)

