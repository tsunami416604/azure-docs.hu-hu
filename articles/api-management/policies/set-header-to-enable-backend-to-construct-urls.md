---
title: Példa az Azure API management házirend - továbbított fejléc hozzáadása |} A Microsoft Docs
description: Az Azure API management házirend-minta - továbbított fejléc hozzáadása a bejövő kérelem engedélyezéséhez a háttérrendszeri API létrehozásához a megfelelő URL-címek mutatja be.
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
ms.openlocfilehash: b857d1780e9734ce891ce2c0ce4bedf50dfe13e9
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871455"
---
# <a name="add-a-forwarded-header"></a>Egy továbbított fejléc hozzáadása

Ez a cikk bemutatja egy Azure API management házirend minta azt mutatja be, a bejövő kérelem engedélyezéséhez a háttérrendszeri API létrehozásához a megfelelő URL-címek továbbított fejléc hozzáadása. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="code"></a>Kód

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)
