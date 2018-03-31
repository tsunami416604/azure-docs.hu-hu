---
title: Az Azure API management házirend minta - használja az OAuth2 engedélyezési az átjáró és a háttérkiszolgáló közötti |} Microsoft Docs
description: Az Azure API management házirend - mutatja be OAuth2 használata a hitelesítéshez, az átjáró és a háttérkiszolgáló között. Bemutatja, hogy az beszerzése olyan hozzáférési jogkivonatot az aad-ben, és továbbítja azt a háttérkiszolgálóra.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: fc896656a4725475fc78cadb5bab54a27cfc02a2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Az átjáró és a háttérkiszolgáló közötti hitelesítési OAuth2 használni

Ez a cikk bemutatja az Azure API management házirend minta azt mutatja be, hogyan használható az OAuth2 engedélyezési az átjáró és a háttérkiszolgáló közötti. Bemutatja, hogy az beszerzése olyan hozzáférési jogkivonatot az aad-ben, és továbbítja azt a háttérkiszolgálóra. 

Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

A következő parancsfájl tulajdonságban {{}} megjelenő tulajdonságok. Tulajdonságok és az API-kezelési házirendek használatával kapcsolatos további tudnivalókért lásd: [ez](../api-management-howto-properties.md) témakör.
 
## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.xml)]

## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

