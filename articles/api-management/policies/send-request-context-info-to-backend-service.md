---
title: Példa API Management-szabályzat – a kérelem környezeti információinak küldése a háttérbeli szolgáltatásnak
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogyan küldheti el a kérések környezeti információit a háttér-szolgáltatásnak.
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
ms.openlocfilehash: 8cef989ca7ce8ee649c4f4aeb579c43bf8a8d6ab
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442405"
---
# <a name="send-request-context-information-to-the-backend-service"></a>A kérés környezetére vonatkozó információk küldése a háttérszolgáltatásnak

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan kell elküldeni a kérések környezeti információit a háttér-szolgáltatásnak. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

