---
title: Példa API Management-szabályzat – kérelem engedélyezése külső hitelesítő használatával
titleSuffix: Azure API Management
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
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442503"
---
# <a name="authorize-requests-using-external-authorizer"></a>Kérelmek hitelesítése külső hitelesítő használatával

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan védheti meg az API-hozzáférést egy külső engedélyező egyéni hitelesítési/engedélyezési logikával. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Hozzáférési korlátozási szabályzatok](../api-management-access-restriction-policies.md)
+ [Házirend-minták](../policy-samples.md)
