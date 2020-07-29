---
title: Példa API Management-szabályzat – hibák küldése a Stackify a naplózáshoz
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogyan adhat hozzá hiba-naplózási szabályzatot, hogy hibákat küldjön a Stackify a naplózáshoz.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442442"
---
# <a name="send-errors-to-stackify-for-logging"></a>Hibák küldése a Stackify a naplózáshoz

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan adhat hozzá hiba-naplózási szabályzatot, hogy hibákat küldjön a Stackify a naplózáshoz. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot az **on-Error** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

