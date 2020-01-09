---
title: Példa API Management-házirend – az üzenet törzse alapján küldött kérelem útvonala
titleSuffix: Azure API Management
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
ms.openlocfilehash: 8cee2c13386a076f0321619754468cfc1e9fb31c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442424"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>A kérés irányítása a törzse méretétől függően

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan irányíthatja át a kérelmeket a törzsük méretétől függően. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További információ a APIM-házirendekről:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

