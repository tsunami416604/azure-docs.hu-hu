---
title: Az Azure API management házirend minta - küldési kérelem környezet adatainak által a háttérszolgáltatáshoz |} Microsoft Docs
description: Az Azure API management házirend minta - kérelem környezeti információkat küldeni a háttérszolgáltatáshoz mutatja be.
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
ms.openlocfilehash: d6cfd6e63dbc8a56179197b2942c52d15539ae74
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285547"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Kérelem környezeti információkat küldeni a háttérszolgáltatáshoz

Ez a cikk bemutatja az Azure API management házirend minta bemutatja, hogyan kérelem környezeti információkat küldeni a háttérszolgáltatáshoz. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

