---
title: Azure API Management-szabályzat mintája – a válasz gyorsítótárazási időtartamának beállítása | Microsoft Docs
description: Azure API Management-szabályzat – példa – bemutatja, hogyan állíthatja be a válasz-gyorsítótár időtartamát a háttér által küldött Cache-Control fejléc maxAge értéke használatával.
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
ms.openlocfilehash: 67d2f334e2088d96543fabd3b99cac71d95630eb
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071973"
---
# <a name="set-response-cache-duration"></a>Válasz-gyorsítótár időtartamának beállítása

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan állíthatja be a válasz-gyorsítótár időtartamát a háttér által küldött Cache-Control fejléc maxAge értéke használatával. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

