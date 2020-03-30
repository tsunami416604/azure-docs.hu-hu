---
title: Minta API-kezelési házirend – A válaszgyorsítótár időtartamának beállítása
titleSuffix: Azure API Management
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan állíthatja be a válaszgyorsítótár időtartamát maxAge érték használatával a háttérrendszer által küldött Cache-Control fejlécben..
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
ms.openlocfilehash: 3101c5695272e8fa6b577ad313897cbc1fa29629
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442394"
---
# <a name="set-response-cache-duration"></a>Válaszgyorsítótár időtartamának beállítása

Ez a cikk egy Azure API-felügyeleti házirend-mintát mutat be, amely bemutatja, hogyan állíthatja be a válaszgyorsítótár időtartamát maxAge érték használatával a háttérrendszer által küldött Cache-Control fejlécben. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirendminták](../policy-samples.md)

