---
title: Minta API-kezelési szabályzat – Hozzáférés engedélyezése a Google OAuth token használatával
titleSuffix: Azure API Management
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan engedélyezheti a végpontokhoz való hozzáférést a Google oauth-jogkivonat-szolgáltatóként való használatával.
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
ms.openlocfilehash: d606d29d84cd5917c74efe188ae02627ad55d4ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442373"
---
# <a name="authorize-access-using-google-oauth-token"></a>Hozzáférés engedélyezése Google OAuth-jogkivonatokkal

Ez a cikk egy Azure API-felügyeleti szabályzatmintát mutat be, amely bemutatja, hogyan engedélyezheti a végpontokhoz való hozzáférést a Google oauth-jogkivonat-szolgáltatóként való használatával. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirendminták](../policy-samples.md)

