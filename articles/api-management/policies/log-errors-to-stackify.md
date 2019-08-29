---
title: Azure API Management-szabályzat – példa – hibák küldése a Stackify a naplózáshoz | Microsoft Docs
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
ms.openlocfilehash: 82aab34a9815f080fe8abb8c1d8b6de66866806e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067580"
---
# <a name="send-errors-to-stackify-for-logging"></a>Hibák küldése a Stackify a naplózáshoz

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan adhat hozzá hiba-naplózási szabályzatot, hogy hibákat küldjön a Stackify a naplózáshoz. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot az **on-Error** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

