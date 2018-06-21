---
title: Az Azure API management-házirendet a minta - tartalmazó egy Korrelációazonosító fejlécet hozzá |} Microsoft Docs
description: Az Azure API management házirend - mutatja be a bejövő kérelem korrelációs azonosítóra tartalmazó fejléc hozzáadása.
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
ms.openlocfilehash: 68f42124369194124ae1f8ebb93834a5be4e0128
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287373"
---
# <a name="add-a-header-containing-a-correlation-id"></a>A korrelációs azonosító tartalmazó fejléc hozzáadása

Ez a cikk bemutatja az Azure API management házirend minta azt mutatja be, a bejövő kérelem korrelációs azonosítóra tartalmazó fejléc hozzáadása. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

