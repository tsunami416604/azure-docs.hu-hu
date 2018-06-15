---
title: Az Azure API management házirend minta - használja az OAuth2 engedélyezési az átjáró és a háttérkiszolgáló közötti |} Microsoft Docs
description: Az Azure API management házirend - mutatja be OAuth2 használata a hitelesítéshez, az átjáró és a háttérkiszolgáló között. Bemutatja, hogy az beszerzése olyan hozzáférési jogkivonatot az aad-ben, és továbbítja azt a háttérkiszolgálóra.
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
ms.openlocfilehash: 240f78bc66af681d0089c45229ab142adf515e60
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933317"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Az átjáró és a háttérkiszolgáló közötti hitelesítési OAuth2 használni

Ez a cikk bemutatja az Azure API management házirend minta azt mutatja be, hogyan használható az OAuth2 engedélyezési az átjáró és a háttérkiszolgáló közötti. Bemutatja, hogy az beszerzése olyan hozzáférési jogkivonatot az aad-ben, és továbbítja azt a háttérkiszolgálóra. 

Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

A következő parancsfájl tulajdonságban {{}} megjelenő tulajdonságok. Tulajdonságok és az API-kezelési házirendek használatával kapcsolatos további tudnivalókért lásd: [ez](../api-management-howto-properties.md) témakör.
 
## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

