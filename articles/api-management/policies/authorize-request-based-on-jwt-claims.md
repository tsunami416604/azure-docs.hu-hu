---
title: Minta API-kezelési házirend – Hozzáférés engedélyezése JWT-jogcímek alapján
titleSuffix: Azure API Management
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan engedélyezheti a hozzáférést egy adott HTTP-metódusok egy Api jwt-jogcímek alapján.
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
ms.openlocfilehash: 1b84854688fbdcc017b16698dfebfe54a7210110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422188"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Hozzáférés engedélyezése JWT-jogcímek alapján

Ez a cikk egy Azure API-felügyeleti szabályzat mintát mutat be, amely bemutatja, hogyan engedélyezheti a hozzáférést egy adott HTTP-metódusokhoz egy JWT-jogcímeken alapuló API-n. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirendminták](../policy-samples.md)

