---
title: Minta API-kezelési házirend – Kérés engedélyezése külső engedélyezővel
titleSuffix: Azure API Management
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan engedélyezi a kérelmeket egy egyéni vagy örökölt hitelesítési/engedélyezési logikát tartalmazó külső engedélyező használatával.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442503"
---
# <a name="authorize-requests-using-external-authorizer"></a>Kérelmek hitelesítése külső hitelesítő használatával

Ez a cikk egy Azure API-felügyeleti házirend-mintát mutat be, amely bemutatja, hogyan biztosíthatja az API-hozzáférést egy külső hitelesítő használatával, amely egyéni hitelesítési/engedélyezési logikát foglal magában. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Hozzáférési korlátozásokra vonatkozó házirendek](../api-management-access-restriction-policies.md)
+ [Házirendminták](../policy-samples.md)
