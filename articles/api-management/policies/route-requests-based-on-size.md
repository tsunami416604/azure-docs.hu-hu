---
title: Az Azure API Management szabályzatának mintája – a kérést a törzse méretétől függően továbbítja | Microsoft Docs
description: Azure API Management-szabályzat mintája – bemutatja, hogyan irányíthatja át a kérelmeket a szerveik méretétől függően.
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
ms.openlocfilehash: f8f282597004dc73d9fe0f49bf4a41e6a80fc37f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072003"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>A kérés irányítása a törzse méretétől függően

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan irányíthatja át a kérelmeket a törzsük méretétől függően. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

