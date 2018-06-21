---
title: Az Azure API management házirend minta - használja az OAuth2 engedélyezési az átjáró és a háttérkiszolgáló közötti |} Microsoft Docs
description: Az Azure API management házirend - mutatja be OAuth2 használata a hitelesítéshez, az átjáró és a háttérkiszolgáló között. Bemutatja, hogyan szerezheti be a hozzáférési jogkivonatot az AAD-tól, majd továbbíthatja azt a háttérrendszernek.
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
ms.openlocfilehash: d064e918d514b9be1b9fa2dbf30c10edf5167908
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287820"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Az átjáró és a háttérkiszolgáló közötti hitelesítési OAuth2 használni

Ez a cikk bemutatja az Azure API management házirend minta azt mutatja be, hogyan használható az OAuth2 engedélyezési az átjáró és a háttérkiszolgáló közötti. Bemutatja, hogyan szerezheti be a hozzáférési jogkivonatot az AAD-tól, majd továbbíthatja azt a háttérrendszernek. 

Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

A következő parancsfájl tulajdonságban {{}} megjelenő tulajdonságok. Tulajdonságok és az API-kezelési házirendek használatával kapcsolatos további tudnivalókért lásd: [ez](../api-management-howto-properties.md) témakör.
 
## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

