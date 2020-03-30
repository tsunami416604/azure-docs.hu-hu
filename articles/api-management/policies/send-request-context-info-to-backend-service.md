---
title: Minta API-kezelési házirend – Kéréskörnyezet adatainak küldése a háttérszolgáltatásnak
titleSuffix: Azure API Management
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan küldhet kérelem környezetadatait a háttérszolgáltatás.
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
ms.openlocfilehash: 8cef989ca7ce8ee649c4f4aeb579c43bf8a8d6ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442405"
---
# <a name="send-request-context-information-to-the-backend-service"></a>A kérés környezetére vonatkozó információk küldése a háttérszolgáltatásnak

Ez a cikk egy Azure API-felügyeleti szabályzat mintát mutat be, amely bemutatja, hogyan küldhet kérelem környezetadatait a háttérszolgáltatásnak. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirendminták](../policy-samples.md)

