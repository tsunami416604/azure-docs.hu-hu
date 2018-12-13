---
title: Példa az Azure API management házirend - korrelációs azonosítót tartalmazó fejléc hozzáadása |} A Microsoft Docs
description: Az Azure API management házirend-minta - mutatja be, a bejövő kérelem egy korrelációs azonosítót tartalmazó fejléc hozzáadása.
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
ms.openlocfilehash: 126701674b6dc529404f5a7854cda9b31c336170
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868429"
---
# <a name="add-a-header-containing-a-correlation-id"></a>A korrelációs azonosítót tartalmazó fejléc hozzáadása

Ez a cikk bemutatja egy Azure API management házirend minta azt mutatja be, hogyan adhat hozzá, a bejövő kérelem egy korrelációs azonosítót tartalmazó fejléc. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)

