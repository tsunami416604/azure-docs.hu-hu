---
title: Minta API-felügyeleti házirend – Képességek hozzáadása háttérszolgáltatáshoz
titleSuffix: Azure API Management
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan adhat hozzá képességeket egy háttérszolgáltatáshoz. Lehetővé teheti például, hogy egy időjárás-előrejelző API-ban a szélesség és hosszúság helyett elég legyen egy hely nevét megadni.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e286f77009a17870332ed5caf1c66c18dd21d020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442471"
---
# <a name="add-capabilities-to-a-backend-service"></a>Képességek hozzáadása háttérszolgáltatáshoz

Ez a cikk egy Azure API-felügyeleti szabályzat mintát mutat be, amely bemutatja, hogyan adhat hozzá képességeket egy háttérszolgáltatáshoz. Lehetővé teheti például, hogy egy időjárás-előrejelző API-ban a szélesség és hosszúság helyett elég legyen egy hely nevét megadni. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirendminták](../policy-samples.md)

