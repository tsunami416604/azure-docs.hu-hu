---
title: Az Azure API management házirend minta - naplózás Stackify küldési hibák |} Microsoft Docs
description: Az Azure API management házirend minta - bemutatja, hogyan hibák küldendő Stackify naplózási hiba naplózási házirend hozzáadása...
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
ms.openlocfilehash: e47a3736e814229e881a314c7cb05054369db1f0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935246"
---
# <a name="send-errors-to-stackify-for-logging"></a>Hibák küldése a Stackify naplózás

Ez a cikk ismerteti, amely bemutatja, hogyan hibák küldendő Stackify naplózási hiba naplózási házirend hozzáadása az Azure API management házirend minta. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **hiba** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Log errors to Stackify.policy.policy.xml)]

## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

