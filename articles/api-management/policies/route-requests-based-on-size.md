---
title: Az Azure API management házirend minta - útvonalat a kérést a törzs mérete alapján |} A Microsoft Docs
description: Az Azure API management házirend-minta - bemutatja, hogyan irányíthatja a kérelmeket azok a szervek mérete alapján.
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
ms.openlocfilehash: 7656401115cc370d6eee60fb9bddb9bcd92e4201
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872206"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Irányítsa át a kérést a törzs mérete alapján

Ez a cikk bemutatja egy Azure API management házirend minta, amely azt ismerteti, hogyan irányíthatja a kérelmeket azok a szervek mérete alapján. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)

