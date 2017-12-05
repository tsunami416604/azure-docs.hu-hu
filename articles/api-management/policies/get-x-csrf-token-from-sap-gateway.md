---
title: "Az Azure API management házirend minta - megvalósítása X-CSRF mintát |} Microsoft Docs"
description: "Az Azure API management házirend minta - bemutatja, hogyan sok API-k által használt X-CSRF mintát végrehajtásához. Ebben a példában csak az SAP-átjáróhoz elő."
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
ms.openlocfilehash: a99ff25f7f8a3851936ba8d28e76eff98b1b0458
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="implement-x-csrf-pattern"></a>Alkalmazzon X-CSRF minta

Ez a cikk bemutatja az Azure API management házirend minta bemutatja, sok API-k által használt X-CSRF mintát megvalósításához. Ebben a példában csak az SAP-átjáróhoz elő. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get X-CSRF token from SAP gateway using send request policy.xml)]

## <a name="next-steps"></a>Következő lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

