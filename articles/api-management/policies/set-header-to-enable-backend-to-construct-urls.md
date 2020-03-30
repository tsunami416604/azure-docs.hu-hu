---
title: Azure API-kezelési házirend-minta – Továbbított fejléc hozzáadása | Microsoft dokumentumok
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan adhat hozzá egy továbbított fejlécet a bejövő kérelemben, hogy a háttérbeli API-t megfelelő URL-címek hez lehessen létrehozni.
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
ms.openlocfilehash: e525029ae8eab086d11126a4e18958423e207aa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70067507"
---
# <a name="add-a-forwarded-header"></a>Átvitt fejléc hozzáadása

Ez a cikk egy Azure API-felügyeleti szabályzat minta, amely bemutatja, hogyan adhat hozzá egy továbbított fejléc et a bejövő kérelem, hogy a háttér-API-t a megfelelő URL-címek létrehozásához. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="code"></a>Kód

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirendminták](../policy-samples.md)
