---
title: Az Azure API management házirend minta - megvalósítása X-CSRF mintát |} Microsoft Docs
description: Az Azure API management házirend minta - bemutatja, hogyan sok API-k által használt X-CSRF mintát végrehajtásához. Ez a példa kifejezetten az SAP-átjáróra vonatkozik.
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
ms.openlocfilehash: 3a2067836a1488d117dced96f3935f2d1f8b1b48
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946023"
---
# <a name="implement-x-csrf-pattern"></a>Alkalmazzon X-CSRF minta

Ez a cikk bemutatja az Azure API management házirend minta bemutatja, sok API-k által használt X-CSRF mintát megvalósításához. Ez a példa kifejezetten az SAP-átjáróra vonatkozik. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

