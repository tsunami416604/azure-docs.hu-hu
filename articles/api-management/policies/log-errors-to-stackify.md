---
title: Az Azure API management házirend minta – naplózási Stackify küldési hibák |} A Microsoft Docs
description: Az Azure API management házirend-minta - bemutatja, hogyan hibák küldendő Stackify naplózási hiba naplózási házirend hozzáadása...
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
ms.openlocfilehash: 07cc83830fe2d467c611622bb66dfbb8c9429c2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60860538"
---
# <a name="send-errors-to-stackify-for-logging"></a>Hibák elküldése a Stackify naplózáshoz

Ez a cikk bemutatja az Azure API management házirend minta, amely bemutatja, hogyan adhat hozzá egy hiba a naplózási házirend a Stackify hibák elküldése a naplózáshoz. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **– hiba** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)

