---
title: Azure API Management-szabályzat – példa – az X-CSRF minta implementálása | Microsoft Docs
description: Azure API Management-szabályzat – példa – bemutatja, hogyan valósítható meg a sok API által használt X-CSRF minta. Ez a példa kifejezetten az SAP-átjáróra vonatkozik.
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
ms.openlocfilehash: 14ea9113bf5712d6ffce356d02abb7224c21771a
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067719"
---
# <a name="implement-x-csrf-pattern"></a>Az X-CSRF minta implementálása

Ez a cikk egy Azure API Management Policy-mintát mutat be, amely bemutatja, hogyan valósítható meg a sok API által használt X-CSRF minta. Ez a példa kifejezetten az SAP-átjáróra vonatkozik. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

