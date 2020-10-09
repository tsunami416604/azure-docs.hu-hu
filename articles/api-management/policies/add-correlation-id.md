---
title: Példa API Management-szabályzat – korrelációs azonosítót tartalmazó fejléc hozzáadása
titleSuffix: Azure API Management
description: Azure API Management-szabályzat – példa – bemutatja, hogyan adhat hozzá egy olyan fejlécet, amely egy korrelációs azonosítót tartalmaz a bejövő kérelemhez.
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
ms.openlocfilehash: 79910aa60602a80cbe79c4ce9899f6ff73fbfde9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "75422254"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Korrelációs azonosítót tartalmazó fejléc hozzáadása

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan adhat hozzá egy olyan fejlécet, amely egy korrelációs azonosítót tartalmaz a bejövő kérelemhez. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

