---
title: Példa API Management-szabályzat – hozzáférés engedélyezése a Google OAuth token használatával
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogyan engedélyezheti a hozzáférést a végpontokhoz a Google használatával OAuth jogkivonat-szolgáltatóként.
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
ms.openlocfilehash: 0f6c9fe2146414f78e90d6ade1a00045cdf3a04f
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078017"
---
# <a name="authorize-access-using-google-oauth-token"></a>Hozzáférés engedélyezése Google OAuth-jogkivonatokkal

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan lehet engedélyezni a végpontokhoz való hozzáférést a Google-OAuth jogkivonat-szolgáltatóként. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-reference.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-reference.md)