---
title: Az Azure API management policy minta – Google OAuth-jogkivonat használatával hozzáférés engedélyezése |} A Microsoft Docs
description: Az Azure API management házirend-minta - mutatja be a végpontok Google OAuth jogkivonat-szolgáltató használatával való hozzáférés engedélyezésére.
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
ms.openlocfilehash: 430f9e57df163ad345f0740e5bd5beca6e892a4c
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869301"
---
# <a name="authorize-access-using-google-oauth-token"></a>Google OAuth-jogkivonat használatával hozzáférés engedélyezése

Ez a cikk bemutatja az Azure API felügyeleti házirend minta azt mutatja be, hogyan engedélyezheti a hozzáférést, a Google használatával, az OAuth jogkivonat-szolgáltató végpontokon. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)

