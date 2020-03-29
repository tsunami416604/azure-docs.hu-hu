---
title: Minta API-kezelési házirend – Hibák küldése a Stackify szolgáltatásba naplózáshoz
titleSuffix: Azure API Management
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan adhat hozzá egy hibanaplózási szabályzatot a Stackify naplózási hibák küldéséhez.Azure API management policy sample – bemutatja, hogyan adhat hozzá egy hibanaplózási szabályzatot a Stackify naplózási hibák küldéséhez.Azure API management policy sample – bemutatja, hogyan adhat hozzá hibanaplózási szabályzatot a Stackify a
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
ms.openlocfilehash: 6662761df005211729dffb16282b8e0a8e2a8444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442442"
---
# <a name="send-errors-to-stackify-for-logging"></a>Hibák küldése a Stackify-be naplózáshoz

Ez a cikk egy Azure API-felügyeleti szabályzat mintát mutat be, amely bemutatja, hogyan adhat hozzá hibanaplózási szabályzatot a Stackify naplózási hibák küldéséhez. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="policy"></a>Szabályzat

Illessze be a kódot **a hibablokkba.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirendminták](../policy-samples.md)

