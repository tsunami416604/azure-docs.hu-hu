---
title: "Az Azure API management-házirendet a minta - adjon meg egy továbbított fejlécet |} Microsoft Docs"
description: "Az Azure API management házirend - mutatja be továbbított fejléc hozzáadása a háttér-API megfelelő URL-címek összeállításához engedélyezi a bejövő kérelemben."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: cc2df914532b6cda37c951b65b243e90b63d57cb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="add-a-forwarded-header"></a>Adjon meg egy továbbított fejlécet

Ez a cikk bemutatja az Azure API management házirend minta bemutatja továbbított fejléc hozzáadása a háttér-API megfelelő URL-címek összeállításához engedélyezi a bejövő kérelemben. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="code"></a>Kód

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)
