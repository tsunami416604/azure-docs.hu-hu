---
title: Az Azure API management házirend mintát,-kérelmet külső authorizer engedélyezése |} Microsoft Docs
description: Az Azure API management házirend minta - bemutatja, hogyan engedélyezze a külső authorizer és az egy egyéni vagy örökölt hitelesítési/engedélyezési logika használatával.
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
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: cce2376c440c233d81198d903709867c5fdca7ec
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839870"
---
# <a name="authorize-requests-using-external-authorizer"></a>Külső authorizer kérések engedélyezésére

Ez a cikk bemutatja az Azure API management házirend minta bemutatja, biztonságossá tétele az API-hozzáférés egy külső authorizer és az egyéni hitelesítési/engedélyezési logika használatával. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Hozzáférési korlátozásokat házirendek](../api-management-access-restriction-policies.md)
+ [Házirend-minták](../policy-samples.md)