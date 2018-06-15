---
title: Az Azure API management házirend minta - válasz gyorsítótár élettartama |} Microsoft Docs
description: Az Azure API management házirend minta - bemutatja, hogyan kell időtartamot válasz gyorsítótár maxAge értéke használatát a háttérrendszer által küldött Cache-Control-fejlécet.
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
ms.openlocfilehash: 8640668ae51c113cc467501b44dbd03b257325c3
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937283"
---
# <a name="set-response-cache-duration"></a>Állítsa be a válasz gyorsítótárazás időtartama

Ez a cikk bemutatja az Azure API management házirend minta bemutatja, hogyan időtartamot válasz gyorsítótár maxAge értéke használatát a háttérrendszer által küldött Cache-Control-fejlécet. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

