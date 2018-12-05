---
title: Az Azure API management házirend minta - megvalósítása X-CSRF minta |} A Microsoft Docs
description: Az Azure API management házirend-minta - sok API-k által használt X-CSRF minta megvalósítását mutatja be. Ez a példa kifejezetten az SAP-átjáróra vonatkozik.
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
ms.openlocfilehash: 2f4d26702443ef3113dad98cde1d13b292fe657a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870010"
---
# <a name="implement-x-csrf-pattern"></a>A minta megvalósítása X-CSRF

Ez a cikk bemutatja egy Azure API management házirend minta azt mutatja be, sok API-k által használt X-CSRF minta megvalósítása. Ez a példa kifejezetten az SAP-átjáróra vonatkozik. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)

