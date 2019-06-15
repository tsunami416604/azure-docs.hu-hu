---
title: Az Azure API management policy minta – JWT jogcímszolgáltatói alapuló hozzáférés engedélyezése |} A Microsoft Docs
description: Az Azure API management házirend-minta - mutatja be az API JWT jogcímszolgáltatói alapján meghatározott HTTP-metódusok elérésének hitelesítéséhez.
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
ms.openlocfilehash: d656cf7c7bed1d40bbde654f9c2484efcc5df25d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61062156"
---
# <a name="authorize-access-based-on-jwt-claims"></a>A JWT jogcímszolgáltatói alapján hozzáférés engedélyezése

Ez a cikk bemutatja egy Azure API management házirend minta azt mutatja be, hogyan engedélyezheti a hozzáférést az API JWT jogcímszolgáltatói alapján meghatározott HTTP-metódusok. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)

