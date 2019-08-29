---
title: Azure API Management-szabályzat mintája – kérések környezeti információinak küldése a háttér-szolgáltatásnak | Microsoft Docs
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
ms.openlocfilehash: cc770ff700155f8ab32bbbd6737c9dad7bc6e664
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067518"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Kérelem környezeti információinak küldése a háttérbeli szolgáltatásnak

Ez a cikk egy Azure API Management-szabályzatot mutat be, amely bemutatja, hogyan kell elküldeni a kérések környezeti információit a háttér-szolgáltatásnak. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

