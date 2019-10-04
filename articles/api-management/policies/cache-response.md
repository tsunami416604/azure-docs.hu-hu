---
title: Azure API Management-szabályzat minta – képességek hozzáadása egy háttér-szolgáltatáshoz | Microsoft Docs
description: Azure API Management-szabályzat mintája – bemutatja, hogyan adhat hozzá képességeket egy háttér-szolgáltatáshoz. Lehetővé teheti például, hogy egy időjárás-előrejelző API-ban a szélesség és hosszúság helyett elég legyen egy hely nevét megadni.
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
ms.openlocfilehash: 705d7e44f64f8dc3bba669cd80dafdab078fcccc
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067774"
---
# <a name="add-capabilities-to-a-backend-service"></a>Képességek hozzáadása egy háttérbeli szolgáltatáshoz

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan adhat hozzá képességeket egy háttér-szolgáltatáshoz. Lehetővé teheti például, hogy egy időjárás-előrejelző API-ban a szélesség és hosszúság helyett elég legyen egy hely nevét megadni. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

