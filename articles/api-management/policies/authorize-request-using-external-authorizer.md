---
title: Azure API Management-szabályzat mintája – engedélyezi a kérést külső engedélyező használatával | Microsoft Docs
description: Azure API Management-szabályzat – példa – bemutatja, hogy a külső engedélyező kérések engedélyezése hogyan engedélyezzen egyéni vagy örökölt hitelesítési/engedélyezési logikát.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 92836e0bfe43a41ad6547c68bc0b9a326528862c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074171"
---
# <a name="authorize-requests-using-external-authorizer"></a>Kérések engedélyezése külső engedélyező használatával

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan védheti meg az API-hozzáférést egy külső engedélyező egyéni hitelesítési/engedélyezési logikával. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Hozzáférési korlátozási szabályzatok](../api-management-access-restriction-policies.md)
+ [Házirend-minták](../policy-samples.md)
