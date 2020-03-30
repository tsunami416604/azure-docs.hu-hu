---
title: Minta API-kezelési házirend – Az üzenettörzs mérete alapján útvonalkérelem
titleSuffix: Azure API Management
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan irányítsa a kérelmeket a testük mérete alapján.
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
ms.openlocfilehash: 8cee2c13386a076f0321619754468cfc1e9fb31c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442424"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Kérés átirányítása a törzs méretétől függően

Ez a cikk egy Azure API-felügyeleti szabályzat mintát mutat be, amely bemutatja, hogyan irányítható a kérelmek a testük mérete alapján. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirendminták](../policy-samples.md)

