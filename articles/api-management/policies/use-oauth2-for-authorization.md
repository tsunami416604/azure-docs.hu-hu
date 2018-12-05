---
title: Az Azure API management házirend minta - használja az OAuth2 engedélyezési az átjáró és a egy háttérrendszer között |} A Microsoft Docs
description: Az Azure API management házirend-minta - bemutatja, hogyan használja az OAuth2 engedélyezési az átjáró és a egy háttérrendszer között. Bemutatja, hogyan szerezheti be a hozzáférési jogkivonatot az AAD-tól, majd továbbíthatja azt a háttérrendszernek.
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
ms.openlocfilehash: 519233cb9e77bf48f67d869a54af771c17c7827e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874558"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Használja az OAuth2 engedélyezési az átjáró és a egy háttérrendszer között

Ez a cikk bemutatja egy Azure API management házirend minta, amely azt ismerteti, hogyan használja az OAuth2 engedélyezési az átjáró és a egy háttérrendszer között. Bemutatja, hogyan szerezheti be a hozzáférési jogkivonatot az AAD-tól, majd továbbíthatja azt a háttérrendszernek. 

Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

A következő szkript tulajdonságban {{}} megjelenő tulajdonságok. Tulajdonságok és az API Management-házirendek használatával kapcsolatos további információkért lásd: [ez](../api-management-howto-properties.md) témakör.
 
## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)

