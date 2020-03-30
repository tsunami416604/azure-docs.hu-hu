---
title: Minta API-kezelési házirend – Korrelációs azonosítót tartalmazó fejléc hozzáadása
titleSuffix: Azure API Management
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan adhat hozzá egy korrelációs azonosítót tartalmazó fejlécet a bejövő kérelemhez.
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
ms.openlocfilehash: 79910aa60602a80cbe79c4ce9899f6ff73fbfde9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422254"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Korrelációs azonosítót tartalmazó fejléc hozzáadása

Ez a cikk egy Azure API-felügyeleti szabályzat minta, amely bemutatja, hogyan adhat hozzá egy címzési azonosítót tartalmazó fejlécet a bejövő kérelemhez. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirendminták](../policy-samples.md)

