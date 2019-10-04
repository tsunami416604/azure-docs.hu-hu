---
title: Azure API Management-szabályzat mintája – válasz tartalmának szűrése | Microsoft Docs
description: Azure API Management-szabályzat – példa – bemutatja, hogyan szűrhetők az adatelemek a válasz adattartalma alapján a kérelemhez társított termék alapján.
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
ms.openlocfilehash: 462db76b06e5071571ae475d420a627e57dad92e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067758"
---
# <a name="filter-response-content"></a>Válasz tartalmának szűrése

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan szűrhetők a kéréshez társított termék alapján a válasz adatelemeinek szűrése. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **kimenő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

