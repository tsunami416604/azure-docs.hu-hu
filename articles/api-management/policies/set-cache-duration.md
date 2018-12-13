---
title: Az Azure API management házirend-minta - válasz-gyorsítótárazás idejének beállítása |} A Microsoft Docs
description: Az Azure API management házirend-minta - bemutatja, hogyan maxAge érték használatával a háttérrendszer által küldött Cache-Control fejléc a válasz-gyorsítótárazás időtartama beállítása...
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 042fab72da2d4b890314b6ee9c7237241b492fba
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869228"
---
# <a name="set-response-cache-duration"></a>Válasz-gyorsítótárazás idejének beállítása

Ez a cikk bemutatja egy Azure API management házirend minta azt mutatja be, hogyan állíthatja be a válasz-gyorsítótárazás időtartama maxAge érték használatával a háttérrendszer által küldött Cache-Control fejléc a. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)

